# 

tags: [教程, Hexo]<br />categories: 写作

---


## 写在前面
**这篇教程能实现什么？**语雀文章更新 → webhook推送 → 触发器触发云函数 → 私有仓库actions拉取文章更新 → 部署到公开仓库 → 托管网站博客自动更新/部署，即语雀端文章更新/发布，博客网站端自动同步。[查看我的博客](https://tszs.info)<br />**为什么选择Hexo博客生成器？**开发者为台湾同胞，文档等中文支持较好；社区、主题资源较为完善。<br />**为什么选择语雀作为编辑端？**语雀在国产在线文档产品中编辑体验较好，支持Markdown。<br />**本教程适用人群？**小白、非程序员等人士，专业和资深人员请选择性阅读。

## 搭建Hexo

### 安装Node.js/Git
如果你的电脑中已经安装Node.js & Git，可直接跳到下节**安装Hexo**

#### 安装Node.js
Node.js 为大多数平台提供了官方的 [安装程序](https://nodejs.org/en/download/)，可下载安装包进行安装，或者：

- Windows：通过 [nvs](https://github.com/jasongin/nvs/)（推荐）或者 [nvm](https://github.com/nvm-sh/nvm) 安装。
- Mac：终端使用 [Homebrew](https://brew.sh/) 安装。
```shell
brew install node
```

#### 安装Git

- Windows：下载并安装 [git](https://git-scm.com/download/win).
- Mac：终端使用 [Homebrew](http://mxcl.github.com/homebrew/) 或者下载 [安装程序](http://sourceforge.net/projects/git-osx-installer/)。
```python
brew install git
```

### 安装Hexo
终端执行安装命令：
```shell
npm install -g hexo-cli
```
安装以后，可以使用以下两种方式（推荐2）执行 Hexo：

1. `npx hexo <command>`
2. 将 Hexo 所在的目录下的 node_modules 添加到环境变量之中即可直接使用 `hexo <command>`，环境变量文件追加内容：
```shell
echo 'PATH="$PATH:./node_modules/.bin"' >> ~/.profile
```

   - [Windows环境变量配置参考案例](https://blog.csdn.net/palmer_kai/article/details/80588594)
   - [Mac环境变量配置参考案例](https://www.jianshu.com/p/8e51bc9cebfa)

### 使用Hexo建站
终端依次输入以下命令，<folder>为要创建的Hexo站点项目文件夹名称：
```shell
hexo init <folder>
cd <folder>
npm install
```
创建完成后，指定文件夹的目录如下：<br />├── _config.yml<br />├── package.json<br />├── scaffolds<br />├── source<br />├  ├── _drafts<br />├  └── _posts<br />└── themes

**_config.yml（根目录）**<br />网站的配置信息文件，可配置除主题内嵌功能外大部分参数，如网站名称等，参考 [Hexo文档｜配置](https://hexo.io/zh-cn/docs/configuration)

**package.json**<br />应用程序/依赖包信息文件

**themes**<br />主题文件夹，Hexo会根据主题来生成静态页面


### 更换主题
Hexo自带了**landscape**主题：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666626999195-5f8c7724-f021-496c-a966-b407a9d59eda.png#clientId=uc8a42427-925b-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=696&id=u50dbbd40&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1392&originWidth=2860&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1395832&status=error&style=none&taskId=uc78d6177-1eac-4d9d-a033-9f7c903dcf2&title=&width=1430)<br />如果不喜欢，可以[参考官网案例](https://hexo.io/themes/)更换其他主题，比如我选择的Cactus：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666627357227-468f06e6-6a0a-4a2d-98c7-a696c140fca9.png#clientId=uc8a42427-925b-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=693&id=u6653ba98&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1386&originWidth=2860&originalType=binary&ratio=1&rotation=0&showTitle=false&size=209712&status=error&style=none&taskId=ufdc6eb6c-417b-4f59-8efc-56494f9cdea&title=&width=1430)<br />操作步骤（以Cactus主题为例）：

1. 在项目**根目录**下执行：
```shell
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```
最后的themes/cactus是指克隆cactus主题到themes文件下。这里有一个坑，在后面部署到GitHub pages时仓库里不能再有仓库，所以克隆完毕后最好删除cactus文件夹里的.git文件夹（已隐藏的文件夹）。

2. 在根目录下的 _config.yml 中设置主题：
```shell
# theme: landscape
theme: cactus
```

3. 一些主题相关的配置项因主题不同而不同，请查阅相关主题文档

### Hexo命令
至此，你完成了最基础的一个Hexo博客搭建，可以在本地进行配置和调试，参考[Hexo文档｜命令](https://hexo.io/zh-cn/docs/commands)<br />比如执行：
```shell
hexo g && hexo s
```
即可在本地浏览器地址 localhost:4000 检查是否搭建成功

## 语雀文章同步到Hexo

### 安装yuque-hexo
项目根目录下命令行安装：
```shell
npm i -g yuque-hexo
```
package.json文件添加以下内容，其中login，repo，token字段需自己配置：
```json
"yuqueConfig": {
  "postPath": "source/_posts",
  "cachePath": "yuque.json",
  "mdNameFormat": "slug",
  "adapter": "hexo",
  "concurrency": 5,
  "baseUrl": "https://www.yuque.com/api/v2",
  "login": "raydon",
  "repo": "tszs",
  "token": "在语雀右上角头像-账户设置-Token新建，把全部的读取权限勾上即可",
  "onlyPublished": true,
  "onlyPublic": true
},
"devDependencies": {
  "yuque-hexo": "^1.9.1"
},
"hexo": {
  "version": "6.3.0"
},
```
其中的login和repo这样对应：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666629248972-fa854d97-8ea6-4b91-97f2-d4380d25bd1d.png#clientId=uc8a42427-925b-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=397&id=u353b7cfc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=794&originWidth=1536&originalType=binary&ratio=1&rotation=0&showTitle=false&size=199735&status=error&style=none&taskId=u976f4cc3-0f36-4553-aa15-7e95cf53a0a&title=&width=768)<br />配置好后，在项目根目录下执行命令，即可拉取语雀对应知识库内容到本地路径/source/_posts：
```shell
yuque-hexo sync
```
之后执行：
```shell
hexo g && hexo s
```
即可在本地浏览器地址 localhost:4000 检查是否成功

#### 语雀图片无法显示的解决
因为语雀图片的防盗链机制，图片会有无法显示的问题，请在 themes/cactus(主题路径)/layout/_partial/head.ejs 中加上一行代码：
```html
<meta name="referrer" content="no-referrer" />  
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666697437716-21e3d55e-a603-4a3d-a50a-325dac47b719.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=274&id=u7c38845a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=548&originWidth=1452&originalType=binary&ratio=1&rotation=0&showTitle=false&size=143680&status=error&style=none&taskId=u9df9191c-c164-44a9-bcd5-20baa3570f9&title=&width=726)<br />**注意：**此行代码可能会导致使用[不蒜子](https://busuanzi.ibruce.info/)等计数器失效，但目前经我测试还OK；另外其他教程有建议将图片上传到图床解决这个问题，但因一下原因，请按需选择：

- 免费图床不可靠
- 收费图床有配置和RMB成本，即使七牛云图床有免费额度，但仍存在Chrome无法加载http图片需要配置SSL证书等问题

### 通过github actions自动更新

#### 准备工作

- 创建一个github公开仓库：用来托管最终生成的静态网站源码，必须以`用户名.github.io`命名，因为github pages 必须以这种格式才能在浏览器通过域名访问。我的公开仓库 `[https://raydon10.github.io](https://raydon10.github.io)`
- 创建一个github私有仓库：用来push托管本地代码，并通过github actions自动（一旦仓库发生变化）部署到上面的公开仓库中，这样以后语雀来源的文章更新就和本地代码没关系了。我的私有仓库 `[https://github.com/raydon10/tszs](https://github.com/raydon10/tszs)`。注意请删掉主题文件夹内的 .git文件夹，因为有 .git就相当于一个仓库，仓库里不能再放仓库，否则本地代码push不到github私有仓库

#### github配置
创建一个token（classic）<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666699098370-aa67d0de-9653-47b8-9184-1ad5427e6683.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=700&id=u31ec7b54&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1400&originWidth=2872&originalType=binary&ratio=1&rotation=0&showTitle=false&size=403010&status=error&style=none&taskId=udd3bba26-1061-4e78-9c62-0163ed85741&title=&width=1436)<br />勾上这几个：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666699597108-2cf776f7-bcb5-436b-8635-45fa47fcb749.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=664&id=u6286e29e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1328&originWidth=1590&originalType=binary&ratio=1&rotation=0&showTitle=false&size=244595&status=error&style=none&taskId=u08976557-2247-4030-a174-42532208039&title=&width=795)<br />创建完的token要记下来，不然以后就看不到了<br />再进到刚才准备工作创建的私有仓库，创建两个sercet：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666699818466-ae2dbfff-0323-45a5-b7b3-bdb8bcd677c5.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=655&id=u7b7c96fa&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1310&originWidth=2832&originalType=binary&ratio=1&rotation=0&showTitle=false&size=303469&status=error&style=none&taskId=u6dabd183-e346-4186-b8a5-d31e7c9d096&title=&width=1416)<br />**GH_REF** 内容是公开仓库/博客地址（不需要https://）：raydon10.github.io<br />**GH_TOKEN** 是上面生成的token（这样做为了防止token在gitHub action代码中暴露）<br />在本地项目文件夹根目录创建目录文件：`.github/workflows/main.yml `，也可以在github私有仓库的Actions选项中创建，但为了本地调试改代码方便，直接在本地文件创建workflows文件，可直接同步到github。<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666700126769-1923ce61-e343-4d38-a492-27a544a3a9ae.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=225&id=u45b3aee8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=450&originWidth=526&originalType=binary&ratio=1&rotation=0&showTitle=false&size=39859&status=error&style=none&taskId=ud6e508cd-3ca5-4c9f-ba83-de81edc6434&title=&width=263)

`main.yml` 填入以下代码：
```yaml
name: Blog CI/CD

on: [push, repository_dispatch]

jobs:
  blog-cicd:
    name: Hexo blog build & deploy
    runs-on: ubuntu-latest
    env:
      TZ: Asia/Shanghai
    steps:
    - name: Checkout codes
      uses: actions/checkout@v2

    - name: Setup node
      uses: actions/setup-node@v1
      with:
        node-version: '12.x'
    - name: Cache node modules
      uses: actions/cache@v1
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

    - name: Install dependencies
      run: |
        npm install hexo-cli -g
        npm install yuque-hexo -g
        npm install
        yuque-hexo sync
        
    - name: Generate files
      run: hexo generate
      
    - name: Deploy blog
      run: |
        git clone "https://${{ secrets.GH_REF }}" deploy_git
        mv ./deploy_git/.git ./public
        cd ./public
        git config user.name "填入自己的github用户名"
        git config user.email "填入自己的github邮箱"
        git add .
        git commit -m "GitHub Actions Auto Builder at $(date +'%Y-%m-%d %H:%M:%S')"
        git push --force --quiet "https://${{ secrets.GH_TOKEN }}@${{ secrets.GH_REF }}" main:main
        

```
通过上面的代码，当仓库发生变化或 repository_dispatch 时会自动触发更新部署到公开仓库（即博客网站）

#### serverless云函数配置
进入 [腾讯云控制台](https://console.cloud.tencent.com/scf/list?rid=1&ns=default) 搜索 云函数，点击新建：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666701055138-f3023008-c2d9-49bc-bec9-cf38b068fef4.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=566&id=uafc5a278&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1132&originWidth=1992&originalType=binary&ratio=1&rotation=0&showTitle=false&size=214927&status=error&style=none&taskId=u73de572c-57a5-4bbf-bb1f-29fa4c41804&title=&width=996)<br />代码内容填写：
```python
# -*- coding: utf8 -*-
import requests
def main_handler(event, context):
    r = requests.post("https://api.github.com/repos/raydon10/tszs/dispatches",
    json = {"event_type": "run-it"},
    headers = {"User-Agent":'curl/7.52.1',
              'Content-Type': 'application/json',
              'Accept': 'application/vnd.github.everest-preview+json',
              'Authorization': 'token 这里是上面创建的github_token'})
    if r.status_code == 204:
        return "This's OK!" 
    else:
        return r.status_code
```
注意用你自己的私有仓库替代上述代码中的仓库地址，'Authorization': 'token 的token不要删掉。<br />点击`测试`，看到“That's OK!”就是成功了；同时在github私有仓库的Actions选项卡下可以看到对应动作，等待动作从黄色变成绿色则说明配置成功了，否则点进去详情看下具体原因具体解决～<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666701477056-cf235e2c-bc2a-4968-92ff-50502816f1a3.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=378&id=u140d5ee2&margin=%5Bobject%20Object%5D&name=image.png&originHeight=756&originWidth=1956&originalType=binary&ratio=1&rotation=0&showTitle=false&size=105989&status=error&style=none&taskId=ub19fc062-919e-498d-b35c-6aaecd7c8fb&title=&width=978)<br />在刚才的函数服务下创建一个触发器（用于接受语雀更新的webhook）：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666701697084-e52624a3-a068-4135-b90e-ce1419355a98.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=685&id=u6b0a8094&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1370&originWidth=2758&originalType=binary&ratio=1&rotation=0&showTitle=false&size=356143&status=error&style=none&taskId=u8236b50d-22f8-4ff8-9e94-cd14a9b21b0&title=&width=1379)<br />选择这个然后提交：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666701843155-522c781a-c5b9-41a6-b100-26fe9d62756a.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=696&id=u387e2cc7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1392&originWidth=1594&originalType=binary&ratio=1&rotation=0&showTitle=false&size=227842&status=error&style=none&taskId=u18fb6418-d0e8-4232-aefe-595bb3878b0&title=&width=797)<br />然后找到刚创建的触发器的访问路径：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666701900213-abc6a744-66e5-40de-b87f-23cd2a5e32e2.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=623&id=u108c6c24&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1246&originWidth=2472&originalType=binary&ratio=1&rotation=0&showTitle=false&size=248369&status=error&style=none&taskId=u2d76f061-8500-48fa-99fd-542317802d0&title=&width=1236)<br />**注意：**腾讯云函数服务可能有一定费用（不高），使用过程需留意。

#### 语雀webhook配置
在作为博客写作端的语雀知识库（没有则新建一个），点击设置：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666702127791-1dc5f165-d2c0-42c1-9c05-c4ae1bd059ad.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=652&id=u485cdfc1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1304&originWidth=1900&originalType=binary&ratio=1&rotation=0&showTitle=false&size=174718&status=error&style=none&taskId=u39b4885d-283e-41ee-ab51-758ed5d950f&title=&width=950)<br />webhook地址填上面的触发器访问地址。

至此，一个**语雀文章更新 → webhook推送 → 触发器触发云函数 → 私有仓库actions拉取文章更新 → 部署到公开仓库**的自动化过程就完成了！自己验证试试吧！

## 使用自己的域名
通过上面的一通操作，可以在自己的github pages页面查看自动化部署的Hexo个人博客网站，域名如 https:// raydon10.github.io，如果想使用自己的域名，可以参考以下内容：

### 腾讯云Web应用托管
进入腾讯云控制台，搜索找到 [Web应用托管](https://console.cloud.tencent.com/webify/index) 新建应用，选择从github仓库，导入我们之前创建的公开仓库（github pages），这样一旦仓库有更新，就会触发Web应用托管自动同步更新内容并构建和部署网站：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666703273467-b735573d-3bed-4d70-9fee-83870797e178.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=674&id=u5349fd2d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1348&originWidth=2856&originalType=binary&ratio=1&rotation=0&showTitle=false&size=454946&status=error&style=none&taskId=u17407610-3de1-4d7c-ad31-29e1206642b&title=&width=1428)<br />**注意：**腾讯云Web应用托管服务有一定费用（但不高），使用中请留意。

腾讯云域名中有.git片段会导致无法自动构建，经过我和腾讯云的沟通他们已经修复这个BUG：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666703777153-5d0b9c42-1290-408d-9238-f364e5656d55.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=606&id=udfb7eba7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1212&originWidth=1610&originalType=binary&ratio=1&rotation=0&showTitle=false&size=268775&status=error&style=none&taskId=u87066c42-a045-435b-b7c1-2990f888149&title=&width=805)

至此我们能通过腾讯云分配给我们的域名进行访问，要使用自己的域名可以进行绑定（需要备案）：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666703522286-553d16e3-9ba7-4cf8-ad13-4762d2bdeb2e.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=682&id=u730da541&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1364&originWidth=2862&originalType=binary&ratio=1&rotation=0&showTitle=false&size=247054&status=error&style=none&taskId=uee07eb28-d943-4772-b3b9-0c158434ab6&title=&width=1431)<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666703560888-e490aadf-90a7-4edf-af69-ef0a42f0480b.png#clientId=u444c3a6b-8b2f-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=693&id=u58894915&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1386&originWidth=2862&originalType=binary&ratio=1&rotation=0&showTitle=false&size=301028&status=error&style=none&taskId=uca55e7b2-eb62-49e9-bc7c-2a74cb68bb9&title=&width=1431)

### 其他私有域名方案
请自行查阅～

## Hexo/Cactus主题的技巧和坑

### Hexo

#### 添加 .gitignore文件
将一些不需要的文件添加到本地项目根目录下的 .gitignore文件（新建/纯文本），如果按上面教程进行的博客搭建，可添加以下内容：
```
.DS_Store
.deploy_git/
public/
source/_posts/
lastGeneratePath.log
db.json
yuque.json
```
这样git push时会忽略这些文件或文件夹，.DS_Store只有Mac系统需添加，其余文件会在github actions执行时重新构建，无需push。

#### 添加 lastGeneratePath.log 文件
在本地项目根目录添加 lastGeneratePath.log 文件，并在 package.json 的 "yuqueConfig" 中写入 `"lastGeneratePath": "lastGeneratePath.log"`，这样每次执行`yuque-hexo sync`同步语雀内容时，只会获取上次更新时间戳之后发生变化的内容。

### Cactus

#### 菜单在Chrome中消失的BUG
在Chromium内核的PC浏览器中，文章正文页如超出屏幕高度，拖动浏览页面菜单消失，回到顶部后不再出现，并且点击菜单按钮也不会出现。解决办法：<br />将 /themes/cactus/source/js/main.js 文件中
```javascript
/**
 * Add a scroll listener to the menu to hide/show the navigation links.
 */
if (menu.length) {
  $(window).on("scroll", function() {
    var topDistance = menu.offset().top;
```
部分的`var topDistance = menu.offset().top`替换为`var topDistance = window.pageYOffset`即可解决。

#### 增加不蒜子统计
效果如图：<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/216826/1666706620175-5fa923b2-0bef-456b-a241-a8d72bf2fc57.png#clientId=u7ce722be-f9b4-4&crop=0&crop=0&crop=1&crop=1&errorMessage=unknown%20error&from=paste&height=147&id=u9fa52a67&margin=%5Bobject%20Object%5D&name=image.png&originHeight=294&originWidth=1486&originalType=binary&ratio=1&rotation=0&showTitle=false&size=42965&status=error&style=none&taskId=ud1d1b4f0-9b9f-4594-9597-e61a92854d7&title=&width=743)

在 /themes/cactus/layout/_partial/footer.ejs 文件末尾增加不蒜子代码：
```javascript
<footer id="footer">
  <div class="footer-left">
    <%= __('footer.copyright') %> &copy;
    <% var endYear = (theme.copyright && theme.copyright.end_year) ? theme.copyright.end_year : new Date().getFullYear() %>
    <% var startYear = (theme.copyright && theme.copyright.start_year) ? theme.copyright.start_year : new Date().getFullYear() %>
    <%= startYear >= endYear ? endYear : startYear + "-" + endYear %>
    <%= config.author || config.title %> 转载请注明出处
  </div>
  <div class="footer-right">
    <nav>
      <ul>
        <% for (var i in theme.nav) { %><!--
       --><li><a href="<%- url_for(theme.nav[i]) %>"><%= __('nav.'+i).replace("nav.", "") %></a></li><!--
     --><% } %>
      </ul>
    </nav>
  </div>
  <div class="busuanzi">
    <% if (theme.busuanzi && theme.busuanzi.enable){ %>
      <!-- 不蒜子统计 -->
      <span id="busuanzi_container_site_pv">
          网站总访问量：<span id="busuanzi_value_site_pv"></span>
      </span>
      <!-- <span class="post-meta-divider">|</span>
      <span id="busuanzi_container_site_uv" style='display:none'>
          本站访客数<span id="busuanzi_value_site_uv"></span>人
      </span> -->
    <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
    <% } %> 
  </div>
</footer>
```
同时在 /themes/cactus/source/css/_partial/footer.styl 文件增加 .busuanzi及以后内容：
```css
@media (min-width: 39rem)
  #footer
    display: flex
    flex-flow: row wrap
    justify-content: space-between
    align-items: center
    align-content: center
    margin-bottom: 20px

    .footer-left
      align-self: flex-start
      margin-right: 20px

    .footer-right
      align-self: flex-end

    .busuanzi
      display: inline-block
      width: 100%
      text-align: left
```
 在 /themes/cactus/_config.yml 追加以下内容：
```yaml
# 是否开启访问量统计功能(不蒜子)
busuanzi:
  enable: true
```


---

> 作者: xxxx  
> URL: http://example.org/pg3i82/  

