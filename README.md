[![Build Status](https://github.com/pqrs-org/Karabiner-DriverKit-VirtualHIDDevice/workflows/CI/badge.svg)](https://github.com/pqrs-org/Karabiner-DriverKit-VirtualHIDDevice/actions)
[![License](https://img.shields.io/badge/license-Public%20Domain-blue.svg)](https://github.com/pqrs-org/Karabiner-DriverKit-VirtualHIDDevice/blob/master/LICENSE.md)

# Karabiner-DriverKit-VirtualHIDDevice

Virtual devices (keyboard and mouse) implementation for macOS using DriverKit.

## Supported systems

-   macOS Big Sur (11.0)
    -   Both Intel-based Macs and Apple Silicon Macs
-   macOS Catalina (10.15)
    -   Intel-based Macs

## Status

-   Implemented:
    -   Extension manager
    -   Virtual HID keyboard
    -   Virtual HID pointing
    -   Virtual HID device client

## Documents

-   [How to be close to DriverKit](DEVELOPMENT.md)
-   [Extracts from xnu](XNU.md)

## Screenshots

-   System Preferenecs (macOS detects the virtual keyboard)<br/><br />
    <img src="docs/images/system-preferences@2x.png" width="668" alt="System Preferences" /><br /><br />
-   Extension manager<br/><br />
    <img src="docs/images/extension-manager@2x.png" width="798" alt="Extension manager" /><br /><br />
-   Client<br/><br />
    <img src="docs/images/client@2x.png" width="480" alt="Client" /><br /><br />

---

## For developers

### How to build

System requirements to build Karabiner-Elements:

-   macOS 10.15+
-   Xcode 11+
-   Command Line Tools for Xcode
-   [XcodeGen](https://github.com/yonaskolb/XcodeGen)

### Note

A provisioning profile which supports `com.apple.developer.driverkit` is required to build a driver extension since Xcode 12.

If you want to start without a valid provisioning profile, use Xcode 11 and
[Karabiner-DriverKit-VirtualHIDDevice v0.11.0](https://github.com/pqrs-org/Karabiner-DriverKit-VirtualHIDDevice/releases/tag/v0.11.0).

### Steps

1.  Gain the DriverKit entitlements to be able to create a provisioning profile which supports `com.apple.developer.driverkit`.
    Specifically, follow the instructions on [Requesting Entitlements for DriverKit Development](https://developer.apple.com/documentation/driverkit/requesting_entitlements_for_driverkit_development)

    Note: This process may take some time to complete on Apple's end.

    If you want to start without the request, use Xcode 11 and Karabiner-DriverKit-VirtualHIDDevice v0.11.0. (See above note)

2.  Create a Developer ID distribution provisioning profile for `org.pqrs.Karabiner-DriverKit-VirtualHIDDevice` with `com.apple.developer.driverkit` entitlement.

    <img src="docs/images/generate-a-provisioning-profile@2x.png" width="921" alt="Generate a Provisioning Profile" />

3.  Replace `CODE_SIGN_IDENTITY` at `src/scripts/codesign.sh` with yours.

    Find your codesign identity by executing the following command in Terminal.

    ```shell
    security find-identity -p codesigning -v
    ```

    The result is as follows.

    ```text
    1) 8D660191481C98F5C56630847A6C39D95C166F22 "Developer ID Application: Fumihiko Takayama (G43BCU2T37)"
    2) 6B9AF0D3B3147A69C5E713773ADD9707CB3480D9 "Apple Development: Fumihiko Takayama (YVB3SM6ECS)"
    3) 637B86ED1C06AE99854E9F5A5DCE02DA58F2BBF4 "Mac Developer: Fumihiko Takayama (YVB3SM6ECS)"
    4) 987BC26C6474DF0C0AF8BEA797354873EC83DC96 "Apple Distribution: Fumihiko Takayama (G43BCU2T37)"
        4 valid identities found
    ```

    Choose one of them (e.g., `8D660191481C98F5C56630847A6C39D95C166F22`) and replace existing `CODE_SIGN_IDENTITY` with yours as follows.

    ```shell
    # Replace with your identity
    readonly CODE_SIGN_IDENTITY=8D660191481C98F5C56630847A6C39D95C166F22
    ```

4.  Replace team identifier, domain and embedded.provisionprofile.

    -   Search `G43BCU2T37` and replace them with your team identifier.

        ```shell
        git grep G43BCU2T37 src/
        ```

    -   Search `org.pqrs` and `org_pqrs`, then replace them with your domain.

        ```shell
        git grep org.pqrs src/
        git grep org_pqrs src/
        ```

    -   Replace `embedded.provisionprofile` file with yours.

        ```shell
        find * -name 'embedded.provisionprofile'
        ```

5.  Build by the following command in terminal.

    ```shell
    cd src
    make
    ```

    `build/Release/Karabiner-DriverKit-ExtensionManager.app` will be generated.
