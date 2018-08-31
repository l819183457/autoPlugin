# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane
# 定义fastlane版本号
fastlane_version “2.46.1” 

# 定义打包平台
default_platform :ios

PROJECT_NAME = "ceshiee"

PLIST_FILE_PATH  = "#{PROJECT_NAME}/Info.plist"

CURRENT_NET_LOCAL =  "90000"
CURRENT_NET_DEV =  "90001"
CURRENT_NET_QA =  "90002"
CURRENT_NET_PRO =  "90003"


CURRENT_CONFIGURATION = "90003"


def set_info_plist_value(path,key,value)
  sh "/usr/libexec/PlistBuddy -c \"set :#{key} #{value}\" #{path}"
end
def set_channel_id(channelId)
    set_info_plist_value(
        "./../#{PLIST_FILE_PATH}",
        'CURRENT_CONFIGURATION',
        "#{CURRENT_CONFIGURATION}"
    )
end


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

def getBanBenList

	puts("*************| 更新build #{"getBanBenList"} |*************")
	
end

#指定项目的scheme名称


# 任务脚本
platform :ios do

	lane :development_build do|options|
	branch = options[:branch]
	
	puts "请选择要打的URL环境：（1: DEV版本=90001，2: QA版本=90002,3: 生产版本=90003）"      
	scheme = STDIN.gets
	if scheme == "1\n" 
  		CURRENT_CONFIGURATION =  CURRENT_NET_DEV;
  		puts "请选择要打的URL环境为: DEV版本）" 
  	elsif scheme == "2\n"
  		 CURRENT_CONFIGURATION =  CURRENT_NET_QA; 
  		 puts "请选择要打的URL环境为: QA版本）"    		     
 	else
  		CURRENT_CONFIGURATION =  CURRENT_NET_PRO; 
  		puts "请选择要打的URL环境为: 生产版本）"     
	end
	puts("*************| 设置环境变量 #{CURRENT_CONFIGURATION }|*************")
	set_channel_id(CURRENT_CONFIGURATION);
	puts("*************| 设置环境变量完成  #{CURRENT_CONFIGURATION } |*************")
	# puts “开始打development ipa”
	# updateProjectBuildNumber #更改项目build号

	#开始打包
	puts("*************| 开始打包 #{CURRENT_CONFIGURATION }|*************")
	gym(
		#输出的ipa名称
		output_name:“#{PROJECT_NAME}_#{get_build_number()}”,
		# 是否清空以前的编译信息 true：是
		clean:true,
		# 指定打包方式，Release 或者 Debug
		configuration:"Release",
		# 指定打包所使用的输出方式，目前支持app-store, package, ad-hoc, enterprise, development
		export_method:"development",
		# 指定输出文件夹
		output_directory:"./fastlane/build",
	)
	puts("*************| 打包结束 #{CURRENT_CONFIGURATION }|*************")	


end
end
