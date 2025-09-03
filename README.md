## 使用lerna实现monorepo
- 安装依赖
```js
mkdir new-monorepo && cd new-monorepo
npm init -y
npm i -g lerna
lerna -v
git init new-monorepo

// Lerna v8 及以上版本必须配合包管理器的 workspaces 使用，否则需要在 lerna.json 里手动指定 packages 路径。
/*解决方法一：使用 workspaces（推荐）
1. 在 package.json 添加 workspaces 配置：
{
  "name": "new-monorepo",
  "version": "1.0.0",
  "private": true,
  "workspaces": [
    "packages/*"
  ]
}
2. 重新运行 lerna init

解决方法二：手动指定 packages
1. 在 lerna.json 添加 packages 字段
{
  "version": "independent",
  "packages": [
    "packages/*"
  ]
}
2. 重新运行 lerna init
*/ 
lerna init
/**
lerna init 在 Lerna v8 及以上版本不会自动生成 packages 文件夹，只会生成 lerna.json 和更新 package.json。
原因：Lerna 现在依赖包管理器的 workspaces 配置（如 package.json 里的 "workspaces": ["packages/*"]），不会主动创建 packages 目录
 */
mkdir packages
cd packages
mkdir demo1
cd demo1
npm init -y

cd ..
mkdir demo2
cd demo2
npm init -y

cd ..
mkdir demo3
cd demo3
npm init -y

cd .. // 退回到主目录
npm install // lerna bootstrap 已被废弃，推荐使用包管理器自带的依赖安装命令（如 npm install、yarn install 或 pnpm install）来完成 workspace 的依赖安装和链接。

// 把npm项目改成pnpm项目
npm i -g pnpm
rm -rf node_modules package-lock.json
// 在项目根目录新建 pnpm-workspace.yaml 文件
packages:
  - 'packages/*'

// pnpm workspace 的设计就是：根目录下执行 pnpm install 后，所有依赖会统一安装在根目录的 node_modules，每个 packages/demo* 子包目录下不会单独生成 node_modules 文件夹。
// pnpm 使用“虚拟链接”机制（symlink），所有依赖都集中在根目录的 node_modules，子包通过软链接访问依赖。
// 这样可以节省空间、加快安装速度，并且保证依赖版本一致。
pnpm install

/*packages 下的子项目依然可以单独运行。
原因说明
虽然 node_modules 只在根目录，但 pnpm 会自动为每个子包建立依赖的软链接（symlink）。
当你在某个子包目录下运行脚本（如 pnpm run start 或 pnpm test），pnpm 会自动解析依赖，和传统 npm 项目一样可以正常工作。
只要你的脚本和依赖声明在各自子包的 package.json 里，直接在子包目录下运行即可。
cd packages/demo1
pnpm run build
*/
// 根目录下创建git仓库
rm -rf new-monorepo/.git  // 删除错误的 .git 目录（如果有）
// 在项目根目录重新初始化 git 仓库
cd /Users/sunruoshui/00-notes/0-workspace/022demos-test/new-monorepo
git init

// 添加并提交文件
git add .
git commit -m "init: first commit"

// 先有一次 Git 提交，Lerna 才能正常发布和管理版本。
lerna publish
```