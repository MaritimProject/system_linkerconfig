# LinkerConfig

## Introduction

Linkerconfig is a program to generate linker configuration based on the runtime
environment. Linkerconfig generates one or more ld.config.txt files and some
other files under /linkerconfig during init. Linker will read this generated
configuration file(s) to find out link relationship between libraries and
executable.

## Inputs

TODO: explain inputs (e.g. /system/etc/public.libraries.txt,
/apex/apex-info-list.xml, ..)

### linker.config.json

Linker configuration file can be used to add extra information while linkerconfig
creates linker configuration with the module. This module can be defined as
'linker_config' from Soong, and it will be translated as protobuf file at build
time.

#### Format

linker.config.json file is in json format which can contain properties as below.

| Property Name | Type | Description                                          |
| ------------- | ---- | ---------------------------------------------------- |
| permittedPaths| List<string> | Additional permitted paths |
| visible       | bool | Force APEX namespace to be visible from all sections if the value is true |

#### Example

```
{
    "permittedPaths" : [ "/a", "/b/c", "/d/e/f"],
    "visible": true
}
```

## Outputs

### /linkerconfig/ld.config.txt & /linkerconfig/*/ld.config.txt

TODO: a few words about the files

Check
[ld.config.format.md](https://android.googlesource.com/platform/bionic/+/master/linker/ld.config.format.md).

### /linkerconfig/apex.libraries.txt

The file describes libraries exposed from APEXes. libnativeloader is the main
consumer of this file.

```
# comment line
jni com_android_foo libfoo_jni.so
public com_android_bar libbar.so:libbaz.so
```

The file is line-based and each line consists of `tag apex_namespace
library_list`.

-   `tag` explains what `library_list` is.
-   `apex_namespace` is the namespace of the apex. Note that it is mangled like
    `com_android_foo` for the APEX("com.android.foo").
-   `library_list` is colon-separated list of library names.
    -   if `tag` is `jni`, `library_list` is the list of JNI libraries exposed
        by `apex_namespace`.
    -   if `tag` is `public`, `library_list` is the list of public libraries
        exposed by `apex_namespace`. Here, public libraries are the libs listed
        in `/system/etc/public.libraries.txt.`