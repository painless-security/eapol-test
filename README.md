# eapol-test
Packages eapol_test from the wpa_supplicant project

The output of this is the eapol-test deb. The main debian package is named wpa-supplicant to match the upstream package name. The wpa-supplicant deb is actually identical to eapol-test, and is best ignored. If the source package is needed, it is named wpa-supplicant.

## Build Procedure
Build with, e.g.,
```shell
debuild -i -us -uc -b
```
or your favorite `pdebuild` equivalent. 
