find_package(Java REQUIRED)

include(UseJava)

set(PACKAGE_NAME im.tox.jtoxcore)

# Output directory for .jar file and java .class files
set(CMAKE_JAVA_TARGET_OUTPUT_DIR "${CMAKE_CURRENT_BINARY_DIR}")

# Since class files are apparently not removed by
# 'make clean', create a list and remove them manually
set(CLASSDIR "${CMAKE_JAVA_TARGET_OUTPUT_DIR}/CMakeFiles/${JAR_TARGET_NAME}.dir")
set(CLEANFILES
    "${CLASSDIR}/im/tox/jtoxcore/JTox.class"
    "${CLASSDIR}/im/tox/jtoxcore/ToxError.class"
    "${CLASSDIR}/im/tox/jtoxcore/ToxException.class"
    "${CLASSDIR}/im/tox/jtoxcore/ToxUserStatus.class"
    "${CLASSDIR}/im/tox/jtoxcore/ToxWorker.class"
    "${CLASSDIR}/im/tox/jtoxcore/FriendExistsException.class"
    "${CLASSDIR}/im/tox/jtoxcore/FriendList.class"
    "${CLASSDIR}/im/tox/jtoxcore/ToxFriend.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnActionCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnConnectionStatusCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnMessageCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnFriendRequestCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnNameChangeCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnReadReceiptCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnStatusMessageCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/OnUserStatusCallback.class"
    "${CLASSDIR}/im/tox/jtoxcore/callbacks/CallbackHandler.class"
    "${JNI_HEADER_LOCATION}/${JNI_HEADER_NAME}"
)
set_directory_properties(PROPERTIES ADDITIONAL_MAKE_CLEAN_FILES "${CLEANFILES}")

# Core source files 
set(JTOX_CORE
    im/tox/jtoxcore/JTox.java
    im/tox/jtoxcore/ToxError.java
    im/tox/jtoxcore/ToxException.java
    im/tox/jtoxcore/ToxUserStatus.java
    im/tox/jtoxcore/ToxWorker.java
    im/tox/jtoxcore/FriendExistsException.java
    im/tox/jtoxcore/FriendList.java
    im/tox/jtoxcore/ToxFriend.java
)

# Callback source files
set(JTOX_CALLBACKS
    im/tox/jtoxcore/callbacks/OnActionCallback.java
    im/tox/jtoxcore/callbacks/OnConnectionStatusCallback.java
    im/tox/jtoxcore/callbacks/OnFriendRequestCallback.java
    im/tox/jtoxcore/callbacks/OnMessageCallback.java
    im/tox/jtoxcore/callbacks/OnNameChangeCallback.java
    im/tox/jtoxcore/callbacks/OnReadReceiptCallback.java
    im/tox/jtoxcore/callbacks/OnStatusMessageCallback.java
    im/tox/jtoxcore/callbacks/OnUserStatusCallback.java
    im/tox/jtoxcore/callbacks/CallbackHandler.java
)

set(JTOX_SOURCE ${JTOX_CORE} ${JTOX_CALLBACKS})

# set version for .jar
set(CMAKE_JAVA_TARGET_VERSION ${JTOXCORE_VERSION})
# .jar target
add_jar(${JAR_TARGET_NAME} ${JTOX_SOURCE})

# Set arguments for javah command
set(JAVAH_ARGS 
    "-classpath"
    "${CLASSDIR}"
    "-o"
    "${JNI_HEADER_LOCATION}/${JNI_HEADER_NAME}"
    "${PACKAGE_NAME}.JTox"
)

# Create custom command for javah, will be executed after add_jar
add_custom_command(
    TARGET ${JAR_TARGET_NAME}
    POST_BUILD
    COMMAND ${Java_JAVAH_EXECUTABLE} ARGS ${JAVAH_ARGS}
)
file(MAKE_DIRECTORY ${JNI_HEADER_LOCATION})

if(${BUILD_JAVADOC} MATCHES y)
    set(JAVADOC_SOURCES
        ${JTOX_SOURCE}
        im/tox/jtoxcore/package-info.java
        im/tox/jtoxcore/callbacks/package-info.java
    )
    create_javadoc(${JAVADOC_TARGET_NAME}
        FILES ${JAVADOC_SOURCES}
        CLASSPATH ${CLASSDIR}
        WINDOWTITLE "${JAR_TARGET_NAME}-${CMAKE_JAVA_TARGET_VERSION}"
        DOCTITLE "<h1>${JAR_TARGET_NAME}-${CMAKE_JAVA_TARGET_VERSION}</h1>"
        AUTHOR TRUE
        USE TRUE
        VERSION TRUE
    )
endif()
