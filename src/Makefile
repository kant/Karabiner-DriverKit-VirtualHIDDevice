all:
	bash scripts/update-info-plist.sh
	xcodegen generate
	xcodebuild -configuration Release SYMROOT="$(CURDIR)/build"
	$(MAKE) -C DriverKit
	mkdir -p build/Release/KarabinerDriverKitVirtualHIDDevice.app/Contents/Library/SystemExtensions
	rsync -a --delete \
		DriverKit/build/Release-driverkit/org.pqrs.driverkit.KarabinerDriverKitVirtualHIDKeyboard.dext \
		build/Release/KarabinerDriverKitVirtualHIDDevice.app/Contents/Library/SystemExtensions
	$(MAKE) codesign

clean:
	rm -rf KarabinerDriverKitVirtualHIDDevice.xcodeproj
	rm -rf build
	$(MAKE) -C DriverKit clean

xcode:
	open KarabinerDriverKitVirtualHIDDevice.xcodeproj

codesign:
	bash scripts/codesign.sh build/Release

verify:
	codesign -vvv -display build/Release/KarabinerDriverKitVirtualHIDDevice.app
	@echo "\n"
	codesign --display --entitlements :- build/Release/KarabinerDriverKitVirtualHIDDevice.app
	@echo "\n"
	codesign -vvv -display build/Release/KarabinerDriverKitVirtualHIDDevice.app/Contents/Library/SystemExtensions/org.pqrs.driverkit.KarabinerDriverKitVirtualHIDKeyboard.dext
	@echo "\n"
	codesign --display --entitlements :- build/Release/KarabinerDriverKitVirtualHIDDevice.app/Contents/Library/SystemExtensions/org.pqrs.driverkit.KarabinerDriverKitVirtualHIDKeyboard.dext

run:
	open build/Release/KarabinerDriverKitVirtualHIDDevice.app

systemextensionsctl-list:
	systemextensionsctl list

log-show-extension-manager:
	log show --predicate 'eventMessage CONTAINS "KarabinerDriverKitVirtualHIDDevice"' --info --debug --last 1h

log-stream-extension-manager:
	log stream --predicate 'eventMessage CONTAINS "KarabinerDriverKitVirtualHIDDevice"'