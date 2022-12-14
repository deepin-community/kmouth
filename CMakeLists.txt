cmake_minimum_required(VERSION 3.16)

project(kmouth)

if(NOT MSVC)
    set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-Wall -Wno-switch -O2 -g")
    set(CMAKE_CXX_FLAGS_RELEASE "-Wall -Wno-switch -O2")
    set(CMAKE_CXX_FLAGS_DEBUG  "-Wall -Wno-switch -g3 -ggdb -O0" CACHE STRING "Debug options." FORCE)
endif(NOT MSVC)

include(FeatureSummary)

set(QT_MIN_VERSION "5.15.0")
set(KF5_MIN_VERSION "5.83.0")


find_package(Qt5 ${QT_MIN_VERSION} REQUIRED COMPONENTS Core PrintSupport TextToSpeech Xml Widgets)

find_package(ECM ${KF5_MIN_VERSION} NO_MODULE REQUIRED)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${ECM_MODULE_PATH})

include(ECMAddAppIcon)
include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDEFrameworkCompilerSettings NO_POLICY_SCOPE)
include(ECMInstallIcons)
include(ECMAddAppIcon)

find_package(KF5  ${KF5_MIN_VERSION} REQUIRED COMPONENTS
    Crash
    Completion
    Config
    ConfigWidgets
    CoreAddons
    DocTools
    I18n
    KIO
    WidgetsAddons
    XmlGui
)
include_directories (${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR} )

add_subdirectory( doc )
add_subdirectory( icons )
add_subdirectory( books )

include_directories(./wordcompletion/  )


########### next target ###############
add_executable(kmouth)

target_sources(kmouth PRIVATE
    ./phrasebook/phrasebookparser.cpp
    ./phrasebook/phrasebookdialog.cpp
    ./phrasebook/initialphrasebookwidget.cpp
    ./phrasebook/phrasebook.cpp
    ./wordcompletion/wordlist.cpp
    ./wordcompletion/wordcompletion.cpp
    ./wordcompletion/wordcompletionwidget.cpp
    ./wordcompletion/dictionarycreationwizard.cpp
    texttospeechconfigurationwidget.cpp
    optionsdialog.cpp
    configwizard.cpp
    phraselist.cpp
    speech.cpp
    texttospeechsystem.cpp
    kmouth.cpp
    main.cpp )

ki18n_wrap_ui(kmouth
    ./phrasebook/phrasebookdialog.ui
    ./wordcompletion/creationsourceui.ui
    ./wordcompletion/creationsourcedetailsui.ui
    ./wordcompletion/kdedocsourceui.ui
    ./wordcompletion/wordcompletionui.ui
    preferencesui.ui
    texttospeechconfigurationui.ui )


target_link_libraries(kmouth
                      KF5::Completion
                      KF5::ConfigCore
                      KF5::Crash
                      KF5::KIOCore
                      KF5::KIOWidgets
                      KF5::I18n
                      KF5::WidgetsAddons
                      KF5::XmlGui
                      Qt::PrintSupport
                      Qt::TextToSpeech
                      Qt::Widgets
                      Qt::Xml )

install(TARGETS kmouth  ${KDE_INSTALL_TARGETS_DEFAULT_ARGS} )


########### install files ###############

install( PROGRAMS org.kde.kmouth.desktop  DESTINATION  ${KDE_INSTALL_APPDIR} )
install( FILES kmouthrc  DESTINATION  ${KDE_INSTALL_CONFDIR} )
install( FILES kmouthui.rc  DESTINATION  ${KDE_INSTALL_KXMLGUI5DIR}/kmouth )
install( FILES phrasebookdialogui.rc DESTINATION ${KDE_INSTALL_KXMLGUI5DIR}/kmouth )
install(FILES org.kde.kmouth.appdata.xml DESTINATION ${KDE_INSTALL_METAINFODIR})

feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)

ki18n_install(po)
if (KF5DocTools_FOUND)
 kdoctools_install(po)
endif()
