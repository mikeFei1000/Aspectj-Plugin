# Aspectj-Plugin
Android 模块依赖Aspectj插件  

对组件化的工程来说，不需要每个模块都加上这么长的Aspectj配置，现在只需要一句代码就可以了

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
//if this module use Aspectj add this replace regular config
apply plugin: 'com.mikefei.plugin'
```

```
//delete this from your gradle
variants.all { variant ->
    if (!variant.buildType.isDebuggable()) {
        log.debug("Skipping non-debuggable build type '${variant.buildType.name}'.")
        return;
    }
 
    JavaCompile javaCompile = variant.javaCompile
    javaCompile.doLast {
        String[] args = ["-showWeaveInfo",
                         "-1.8",
                         "-inpath", javaCompile.destinationDir.toString(),
                         "-aspectpath", javaCompile.classpath.asPath,
                         "-d", javaCompile.destinationDir.toString(),
                         "-classpath", javaCompile.classpath.asPath,
                         "-bootclasspath", project.android.bootClasspath.join(File.pathSeparator)]
        log.debug "ajc args: " + Arrays.toString(args)
 
        MessageHandler handler = new MessageHandler(true);
        new Main().run(args, handler);
        for (IMessage message : handler.getMessages(null, true)) {
            switch (message.getKind()) {
                case IMessage.ABORT:
                case IMessage.ERROR:
                case IMessage.FAIL:
                    log.error message.message, message.thrown
                    break;
                case IMessage.WARNING:
                    log.warn message.message, message.thrown
                    break;
                case IMessage.INFO:
                    log.info message.message, message.thrown
                    break;
                case IMessage.DEBUG:
                    log.debug message.message, message.thrown
                    break;
            }
        }
    }
}

```
