## Unikraft 自动部署

使用Github Actions在 [Unikraft](https://console.unikraft.cloud) 上自动部署服务

### 部署步骤

1：点击 Use this template ➡ Create a new repostory 创建一个私密项目,名称随意

2：在Actions菜单允许 `I understand my workflows, go ahead and enable them` 按钮

3: 击下方文件名直达文件
- [app.py](./argox/app.py)

4: 修改`app.py`文件里 30到52 行中添加需要的环境变量，不需要的留空

5: 填写好变量后全选代码剪切到 https://obf.eooce.com 混淆,将混淆后的代码粘贴到app.py里保存

6：`/argox/index.html` 文件为前端伪装网页,可以使用AI生成纯html代码替换,不动使用默认也可,建议替换

7：去actions菜单,选择左边的工作流，右边点击`Run workflow`按钮选择地区即可自动部署

8：大约等待3~5分钟左右运行完成，点开工作流运行过程,找到 `Get Application Information` 里的App URL访问获取订阅信息

### 注意事项
1. TOKEN 无法修改，也是唯一的，不要泄露
2. 每个账号限部署两个App,可以同地区或不同的地区
3. app.py里的{SUB_PATH}订阅token尽量修改
4. 可以在 → Settings → Secrets and variables → Actions → New repository secret 添加长期使用的 `TOKEN` 变量

### 手动部署步骤
1. 安装CLI
```
curl --proto '=https' --tlsv1.2 -fsSL https://unikraft.com/cli/install.sh | sh
export PATH="/root/.local/bin:$PATH"
```
2. 登录,{API_KEY}替换为自己的
```
eoho "{API_KEY}" | unikraft logn --token -
```
3. 创建存储盘
```
unikraft volumes create --name 硬盘名称 --size 512MiB --metro 地区码
```
示例: unikraft volumes create --name sgdisk --size 512MiB --metro sin

4. 构建镜像, 先把打包镜像的文件放在当前目录里,文件里填好变量混淆
```
unikraft images build . --output your-org-name/image-name:tag 
```
示例: unikraft images build . --output eooce/myapp:latest

5. 部署项目,使用上一步构建的镜像
```
unikraft run --metro 地区码 --image your-org-name/image-name:tag -n 项目名称 -p 443:3000/http+tls -m 512MiB -v 创建的硬盘名称:/data --autostart
```
示例: unikraft run --metro sin --image eooce/myapp:latest -n sgdsb -p 443:3000/http+tls -m 512MiB -v sgdisk:/data --autostart


