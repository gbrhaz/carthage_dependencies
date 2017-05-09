# frozen_string_literal: true

require 'xcodeproj'

# /usr/local/bin/rbenv exec bundle exec rake carthage:copy_frameworks
PLATFORM = 'ios'

SOURCE_DEPENDENCIES = [
  "Alamofire",
  "AlamofireNetworkActivityIndicator",
  "ReactiveCocoa",
  "ReactiveSwift",
  "Result",
  "Swinject",
  "SwinjectStoryboard",
  "SkyFloatingLabelTextField",
  "Cartography",
  "KeychainAccess",
  "OAStackView",
  "TTTAttributedLabel",
  "PhoneNumberKit",
  "SVProgressHUD",
  "RNCryptor",
  "ObjectMapper",
  "MGSwipeTableCell",
  "netfox",
  "IQKeyboardManager",
  "emitter-kit",
  "Async",
  "facebook-sdk-swift",
  "facebook-ios-sdk",
]

BINARY_DEPENDENCIES = [ "pushwoosh-ios-sdk" ]

FRAMEWORK_DEPENDENCIES = [
  "Pushwoosh.framework",
  "Bolts.framework",
  "FBSDKCoreKit.framework",
  "FacebookCore.framework",
  "Async.framework",
  "EmitterKit.framework",
  "CoreActionSheetPicker.framework",
  "IQKeyboardManagerSwift.framework",
  "netfox_ios.framework",
  "MGSwipeTableCell.framework",
  "SkyFloatingLabelTextField.framework",
  "ObjectMapper.framework",
  "SVProgressHUD.framework",
  "RNCryptor.framework",
  "PhoneNumberKit.framework",
  "TTTAttributedLabel.framework",
  "OAStackView.framework",
  "KeychainAccess.framework",
  "Cartography.framework",
  "ReactiveCocoa.framework",
  "ReactiveSwift.framework",
  "Result.framework",
  "Swinject.framework",
  "SwinjectStoryboard.framework",
  "Alamofire.framework",
  "AlamofireNetworkActivityIndicator.framework",
]

def framework_paths
  project_path = 'WorldRemit.xcodeproj'
  project = Xcodeproj::Project.open(project_path)
  target = project.targets.first
  input_paths = target.shell_script_build_phases[3].input_paths
  input_paths.map { |path| path.gsub!('$(SRCROOT)', Dir.pwd) }
end

def frameworks_exist?
  framework_paths.each do |path|
    return false unless File.exists?(path) and File.directory?(path)
  end
end

def carthage_frameworks
  framework_paths.map { |path| Pathname.new(path).basename  }
end

def list_of_frameworks_to_archive
  frameworks = Array.new
  Dir.glob("Carthage/Build/iOS/*.framework") { |file|
    name = File.basename(file, ".*")
    frameworks.push(name)
  }
  frameworks
end

namespace "carthage" do
  desc 'Check the project’s dependencies'
  task :check_dependencies do
    fail unless frameworks_exist?
  end

  desc 'Build the project’s dependencies'
  task bootstrap: :update do
  end

  desc 'Update the project’s dependencies.'
  task :update do
    puts "Updating Carthage dependencies…"
    # next if frameworks_exist?
    # update_success = system "carthage update --platform #{PLATFORM} --no-use-binaries --cache-builds"
    update_success = system "carthage bootstrap --platform #{PLATFORM} --no-use-binaries"
    fail unless update_success
    binary_success = system "carthage update #{BINARY_DEPENDENCIES.join(' ')}"
    fail unless binary_success
  end

  desc 'Update the project’s dependencies.'
  task :archive do
    puts "Updating Carthage dependencies…"
    frameworks = list_of_frameworks_to_archive.join(" ")  #carthage_frameworks
    system "carthage archive #{frameworks} --output carthage_dependencies.framework.zip"
  end

  desc 'Clean Carthage dependencies'
  task :clean do
    info "Cleaning development dependencies…"

    # Remove build directory symlinks
    Dir['Carthage/Checkouts/*'].each do |path|
      next unless File.symlink?(path)
      build_dir = "#{path}/Carthage/Build"
      if File.symlink?(build_dir)
        system "rm -f #{build_dir}"
      end
    end

    puts "Cleaning Carthage dependencies…"
    system 'rm -rf Carthage'

    puts "Clean!"
  end
end

task default: %w[carthage:update]
