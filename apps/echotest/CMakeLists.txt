cmake_minimum_required(VERSION 3.7.2)

project(exchangestring C)

CAmkESAddImportPath(interfaces)
CAmkESAddImportPath(templates)
CAmkESAddImportPath(components)

add_subdirectory(components/main)

DeclareCAmkESRootserver(echotest.camkes)
