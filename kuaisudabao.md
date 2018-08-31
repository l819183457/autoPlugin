使用fastlane 快速打包教程
-------------------------------------
# 功能完善
## 已经完善的功能
	* 基本打包流程  能生成安装包

## 下一步要实现的功能
	* 更好的配置证书(猜想 可能是应为我将证书变成手动的,所以她无法识别证书环境)
	* 尝试打包到禅道
******************************************************	
# 环境配置
## 检查是否安装 ruby
   	* ruby -v  查看版本信息
## 查看数据元  必须是 https://gems.ruby-china.com/ 记一下org结尾的网站过期了,需要换成.com的网站
	* gem sources   查看数据源
	* gem sources -r url地址   删除数据源
	* gem sources -a url地址   添加数据源
## 检查Xcode命令行工具是否安装 支持命令打包运行
	* xcode-select --install  检查是否安装
	**  已经安装提示code-select: error: command line tools are already installed, use "Software Update" to install updates
## 安装Fastlane
	* sudo gem install fastlane --verbose
	**  出现错误  ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted - /usr/bin/rougify
    *** 解决的方案   sudo gem install -n /usr/local/bin fastlane
## 检查Fastlane是否正确安装
	*fastlane --version  查看版本号
# Fastlane配置  每个项目的
## Fastlane 初始化
	* fastlane init  
## Fastlane 输入内容
    * puts "请选择要打的URL环境：（1: DEV版本=90001，2: QA版本=90002,3: 生产版本=90003）"      
      scheme = STDIN.gets
## 修改项目工程配置
	* Settings -> Versioning -> Current Project Version 添加版本号方便获取
	* Settings -> Versioning -> Versioning System 选择Apple Generic  TODO  不知道什么意思到时候补充
## 执行方法
	* fastlane development_build   代码里的类
## 代码实现 demo

    # 定义fastlane版本号
    fastlane_version “2.46.1” 
    
    # 定义打包平台
    default_platform :ios
    
    def updateProjectBuildNumber
    
    currentTime = Time.new.strftime("%Y%m%d")
    build = get_build_number()
    if build.include?"#{currentTime}."
    # => 为当天版本 计算迭代版本号
    lastStr = build[build.length-2..build.length-1]
    lastNum = lastStr.to_i
    lastNum = lastNum + 1
    lastStr = lastNum.to_s
    if lastNum < 10
    lastStr = lastStr.insert(0,"0")
    end
    build = "#{currentTime}.#{lastStr}"
    else
    # => 非当天版本 build 号重置
    build = "#{currentTime}.01"
    end
    puts("*************| 更新build #{build} |*************")
    # => 更改项目 build 号
    increment_build_number(
    build_number: "#{build}"
    )
    end
    
    #指定项目的scheme名称
    scheme=“TestCI”
    #蒲公英api_key和user_key
    api_key=“”
    user_key=“”
    
    # 任务脚本
    platform :ios do
    lane :development_build do|options|
    branch = options[:branch]
    
    puts “开始打development ipa”
    
    updateProjectBuildNumber #更改项目build号
    
    # 开始打包
    gym(
    #输出的ipa名称
    output_name:”#{scheme}_#{get_build_number()}”,
    # 是否清空以前的编译信息 true：是
    clean:true,
    # 指定打包方式，Release 或者 Debug
    configuration:"Release",
    # 指定打包所使用的输出方式，目前支持app-store, package, ad-hoc, enterprise, development
    export_method:"development",
    # 指定输出文件夹
    output_directory:"./fastlane/build",
    )
    
    puts "开始上传蒲公英"
    # 开始上传蒲公英
    pgyer(api_key: “#{api_key}”, user_key: “#{user_key}”)
    
    end
    end


# 知识点主要来源
 	*  https://www.jianshu.com/p/0a113f754c09
