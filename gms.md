### 参考下面的方式，注意系统里面只允许一个apk 安装程序，gms的和aosp 默认的只能选一个
```shell
###############################################################################
# GMS home folder location
# Note: we cannot use $(my-dir) in this makefile
ANDROID_PARTNER_GMS_HOME := vendor/google

# GMS mandatory core packages
PRODUCT_PACKAGES := \
    ConfigUpdater \
    GoogleBackupTransport \
    GoogleExtServices \
    GoogleExtShared \
    GoogleFeedback \
    GoogleLoginService \
    GoogleOneTimeInitializer \
    GooglePartnerSetup \
    GooglePrintRecommendationService \
    GoogleServicesFramework \
    GoogleContactsSyncAdapter \
    GoogleTTS \
    GmsCore \
    Phonesky 
#  GoogleCalendarSyncAdapter \
#    SetupWizard \
#    WebViewGoogle

#GooglePackageInstaller 

# GMS mandatory libraries
PRODUCT_PACKAGES += \
    com.google.android.maps.jar \
    com.google.android.media.effects.jar

# Overlay For GMS devices
$(call inherit-product, device/sample/products/backup_overlay.mk)
$(call inherit-product, device/sample/products/location_overlay.mk)
PRODUCT_PACKAGE_OVERLAYS += $(ANDROID_PARTNER_GMS_HOME)/products/gms_overlay

# Configuration files for GMS apps
PRODUCT_COPY_FILES += \
    $(ANDROID_PARTNER_GMS_HOME)/etc/preferred-apps/google.xml:system/etc/preferred-apps/google.xml \
    $(ANDROID_PARTNER_GMS_HOME)/etc/sysconfig/google.xml:system/etc/sysconfig/google.xml

# GMS mandatory application packages
# Note: Duo is mandatory for telephony devices, whereas Hangouts is for non-telephony devices.
PRODUCT_PACKAGES += \
    Chrome \
    YouTube
#    Velvet \
#    Music2 \
#    Photos \
#    Videos \
#    Drive 
#    Gmail2 
#    Duo 
#    Hangouts 
#    Maps 
# GMS optional application packages
#PRODUCT_PACKAGES += \
#    Books \
#    CalendarGoogle \
#    CloudPrint \
#    DeskClockGoogle \
#    DMAgent \
#    FaceLock \
#    LatinImeGoogle \
#    PlayGames \
#    PlusOne \
#    TagGoogle \
#    talkback \
#    AndroidPay

#PRODUCT_PACKAGES += \
#    EditorsDocs \
#    EditorsSheets \
#    EditorsSlides \
#    Keep \
#    Newsstand

# More GMS optional application packages
#PRODUCT_PACKAGES += \
#    CalculatorGoogle 
#    GooglePinyinIME 
#    GoogleHindiIME \
#    Messenger \
#    JapaneseIME \
#    KoreanIME \
#    NewsWeather
# GMS sample application packages
#PRODUCT_PACKAGES += \
#    GmsSampleLayout

# Overrides
PRODUCT_PROPERTY_OVERRIDES += \
    ro.setupwizard.require_network=OPTIONAL \
    ro.setupwizard.mode=OPTIONAL \
    ro.com.google.gmsversion=7.1_r3
```
