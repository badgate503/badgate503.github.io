---
title: Z-Score 浙大人专属查分/通知软件
date: 2023-07-09 22:36:17
updated: 2023-07-10 09:55:12
index_img: /img/idxzscore.png
categories: 技术
tags: [浙江大学,Python,网页,程序]
---

# Z-Score 浙大人专属查分/通知软件
## 背景
![](/img/zscore2.png)
查分，是三墩镇职校每位学生在期末周后喜闻乐见的娱乐活动。但是，钉钉工作台中“成绩查询”功能却常常因太多人同时访问而无法加载前端界面，令人无语凝噎。同时，在某门课程出分后不能立即接收通知，容易影响我们在钉钉群中发送“谢谢老师🌹🌹”的速度和首位度，进而严重破坏师生关系。  
## 安装
“**Z-Score**”致力于解决以上症（zhēng）结，只需通过简单的 
```bash
$ git clone https://github.com/badgate503/z-score
``` 
指令（可能对您的网络环境有特殊要求），我们即可将Z-Score部署到本地。接着，您既可以自行对 `Zscore.py` 进行编译，也可以直接运行 `dist\zscore.exe` 程序以启动Z-Score。
![](/img/zscore1.png)
## 配置
本程序仅适用于浙江大学本科生，您应有浙江大学教务网（ http://jwbinfosys.zju.edu.cn/ ）的账号和密码。
### 登录
请先输入 `/login` 指令进行登录，分别输入您的教务网账号和密码并进行联机验证，如果您选择"保存账号与密码"，您的账号与密码将会被保存在本地，并且此后打开软件无需再次输入账号密码。  
### 从教务网获取数据
若您是首次使用本程序，在登录成功后将自动从教务网加载一次成绩数据。在登录后，您可以使用 `/refresh` 指令从教务网更新成绩记录。同时，程序的自动更新功能默认开启，默认每隔30s从教务网刷新一次数据。您可以通过 `/config autoupdt switch` 来开启/关闭该功能。
### 配置自动通知
本程序可通过WebHook功能向钉钉机器人发送成绩通知，在首次使用本程序时应当先申请钉钉机器人获取WebHook链接，再通过  `/config notify webhook [url]` 指令设置WebHook链接。使用 `/config notify switch` 指令开启通知功能，每次开启/关闭均会发送通知。程序每次刷新成绩时，若检测到有新记录，就会把课程的课程名、分数、绩点信息通过钉钉机器人发送给您。
## 其余功能
### 均绩计算
每次对成绩进行更新操作均会自动计算均绩。同时，您也可以通过 `/display all [semester]` 显示某个学期的均绩，例如，输入
```
/display all 2022-2023-2
```
将会输出（若省略最后的“-2”则输出2022-2023学年数据）
```bash
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
  ＞2022-2023 春夏学期
  修读课程数：XX
  修读总学分：XX
  学期均绩：X.XXXXXXXXXXXXXXX
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```
### 查看单门课程成绩
输入 `/display detail [filter]` 指令，您可查看经 `filter` 条件筛选后的部分课程成绩。其中， `filter` 可以是学期信息，也可以是以下几种条件之一：
| `filter` |含义|
|:---:|:---:|
| `gpe X` |绩点等于 `X` |
| `gpl X` |绩点低于 `X` |
| `gpg X` |绩点高于 `X` |
| `contain X` |课程名含 `X` |
## 代码分析
### 网页自动化
本程序使用 `Selenium` 插件实现网页自动化功能，用户在登录后，将自动执行网页自动化流程：
```python
web.find_element(By.XPATH, '//*[@id="username"]').send_keys(uname)
web.find_element(By.XPATH, '//*[@id="password"]').send_keys(upwd, Keys.ENTER)
```
在浙江大学统一认证页面输入用户提供的账号与密码以尝试登录，由于登录成功后页面将会发生跳转，通过检测 `Url` 是否改变即可判断是否登录成功。登录成功后，将继续执行以下流程：
```python
web.find_element(By.XPATH, '//*[@id="Button1"]').click()
web.find_element(By.XPATH, '//*[@id="xsmain_kscj.htm"]').click()
web.find_element(By.XPATH, '//*[@class="iconcjcx"]/a').click()
web.switch_to.window(web.window_handles[1])
web.find_element(By.ID, 'Button2').click()
```
获取成绩页面。  
![](/img/zscore10.png "成绩页面")
### 数据更新
本程序采用一个字典数组储存包括课程代码、课程名、成绩、学分、绩点信息。
```python
dataList = {}
dataList['serial'] = []
dataList['name'] = []
dataList['score'] = []
dataList['credit'] = []
dataList['grdpnt'] = []
```
每次从教务网更新成绩时，会先利用网页自动化点击“在校学习成绩查询”按钮以刷新页面，再执行以下代码：
```python
table = web.find_element(By.ID, "DataGrid1")
rows = table.find_elements(By.TAG_NAME ,"tr")
dataList['serial'].clear()
dataList['name'].clear()
dataList['score'].clear()
dataList['credit'].clear()
dataList['grdpnt'].clear()
for row in rows:
    if isFirst:
        isFirst = False
        continue
    cells = row.find_elements(By.TAG_NAME ,"td")
    if cells[2].text == "弃修":
        continue
    if  orginScoreIndex.count(cells[0].text) == 0:
        newScoreIndex.append(cells[0].text)
    dataList['serial'].append(cells[0].text)
    dataList['name'].append(cells[1].text)
    dataList['score'].append(int(cells[2].text))
    dataList['credit'].append(float(cells[3].text))
    dataList['grdpnt'].append(float(cells[4].text))
```
其中，数组 `newScoreIndex` 记录了所有新记录的课程代码，布尔值 `isFirst` 将会抛弃表头内容。从代码中也可看出，程序将会自动跳过弃修课程，以免其以有碍观瞻的形式出现在成绩列表中。
### WebHook
通过以下代码，程序将向WebHook为 `uWebHook` 的钉钉机器人发送通知。
```python
def sendNote(text):
    webhook = uWebHook
    header = {
        "Content-Type": "application/json",
        "Charset": "UTF-8"
    }
    message ={
        "msgtype": "markdown",
        "markdown": {
            "title":"成绩通知",
            "text": text
        },

        "at": {

            "isAtAll": True
        }
    }
    message_json = json.dumps(message)
    info = requests.post(url=webhook,data=message_json,headers=header)
```
其中， `text` 内容为markdown格式。
## 后记
该程序是我本人第一个用Python写的程序，尽管先前从未写过Python，但利用之前学的C#、JS等，再查阅Runoob、CSDN等网站，我也仅花了一天时间完成该程序（很多时间在调整界面、颜色）。我不得不感叹：用别人造好的轮子是件美事。