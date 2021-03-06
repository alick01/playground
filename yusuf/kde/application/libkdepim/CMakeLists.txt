cmake_minimum_required(VERSION 2.8.12)

project(libkdepim)

find_package(ECM 5.19.0 CONFIG REQUIRED)
set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH})
set(LIBRARY_NAMELINK)
include(GenerateExportHeader)
include(ECMSetupVersion)
include(ECMGenerateHeaders)
include(ECMGeneratePriFile)
include(ECMPackageConfigHelpers)
include(ECMSetupVersion)
include(FeatureSummary)
include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDEFrameworkCompilerSettings NO_POLICY_SCOPE)
include(ECMQtDeclareLoggingCategory)
include(ECMAddTests)

set(KF5_VERSION "5.19.0")
set(LIBKDEPIM_LIB_VERSION "5.2.0")
set(KDEPIMLIBS_LIB_VERSION "5.2.0")
set(QT_REQUIRED_VERSION "5.4.0")
set(KMIME_LIB_VERSION "5.2.0")
set(KLDAP_LIB_VERSION "5.2.0")
set(KCONTACTS_LIB_VERSION "5.2.0")
set(AKONADISEARCH_LIB_VERSION "5.0.42")
set(AKONADI_VERSION "5.2.0")

find_package(KF5Akonadi ${AKONADI_VERSION} CONFIG REQUIRED)
find_package(Qt5 ${QT_REQUIRED_VERSION} CONFIG REQUIRED Widgets Test UiTools DBus Network)
find_package(KF5I18n ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5Mime ${KMIME_LIB_VERSION} CONFIG REQUIRED)
find_package(KF5KCMUtils ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5Codecs ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5Completion ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5Wallet ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5ItemViews ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5IconThemes ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5ItemViews ${KF5_VERSION} CONFIG REQUIRED)
find_package(KF5Ldap ${KLDAP_LIB_VERSION} CONFIG REQUIRED)
find_package(KF5Contacts ${KCONTACTS_LIB_VERSION} CONFIG REQUIRED)
find_package(KF5AkonadiContact ${KDEPIMLIBS_LIB_VERSION} CONFIG REQUIRED)

find_package(KF5AkonadiSearch ${AKONADISEARCH_LIB_VERSION} CONFIG REQUIRED)
set_package_properties(KF5AkonadiSearch PROPERTIES DESCRIPTION "The Akonadi Search libraries" URL "http://www.kde.org" TYPE REQUIRED PURPOSE "Provides search capabilities in KMail and Akonadi")

find_package(Qt5Designer CONFIG)
set_package_properties(Qt5Designer PROPERTIES
   PURPOSE "Required to build the Qt Designer plugins"
   TYPE OPTIONAL
)

ecm_setup_version(${LIBKDEPIM_LIB_VERSION} VARIABLE_PREFIX LIBKDEPIM
                        VERSION_HEADER "${CMAKE_CURRENT_BINARY_DIR}/libkdepim_version.h"
                        PACKAGE_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/KF5LibkdepimConfigVersion.cmake"
                        SOVERSION 5
)
remove_definitions( -DQT_NO_CAST_FROM_BYTEARRAY )

########### CMake Config Files ###########
set(CMAKECONFIG_INSTALL_DIR "${KDE_INSTALL_CMAKEPACKAGEDIR}/KF5Libkdepim")

ecm_configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/KF5LibkdepimConfig.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/KF5LibkdepimConfig.cmake"
  INSTALL_DESTINATION  ${CMAKECONFIG_INSTALL_DIR}
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/KF5LibkdepimConfig.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/KF5LibkdepimConfigVersion.cmake"
  DESTINATION "${CMAKECONFIG_INSTALL_DIR}"
  COMPONENT Devel
)

ecm_configure_package_config_file(MailTransportDBusServiceConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/MailTransportDBusServiceConfig.cmake
  PATH_VARS KDE_INSTALL_DBUSINTERFACEDIR
  INSTALL_DESTINATION "${CMAKECONFIG_INSTALL_PREFIX}/MailTransportDBusService")

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/MailTransportDBusServiceConfig.cmake
  DESTINATION "${CMAKECONFIG_INSTALL_PREFIX}/MailTransportDBusService")

install(EXPORT KF5LibkdepimTargets DESTINATION "${CMAKECONFIG_INSTALL_DIR}" FILE KF5LibkdepimTargets.cmake NAMESPACE KF5::)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/libkdepim_version.h
  DESTINATION ${KDE_INSTALL_INCLUDEDIR_KF5} COMPONENT Devel
)



add_subdirectory(src)
if (BUILD_TESTING)
   add_subdirectory(tests)
endif()

install( FILES libkdepim.categories DESTINATION ${KDE_INSTALL_CONFDIR} )

feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)
