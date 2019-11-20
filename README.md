# Aspectj-Plugin
Android 模块依赖Aspectj插件

### root project build.gradle
```
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
        ...
    }
}

dependencies {
        classpath 'com.mikefei.project.plugin:plugin-cooker:1.0.6'
}
```
### Use Aspectj module build.gradle
```
//if this module use Aspectj add this
apply plugin: 'com.mikefei.plugin'
```
