# Java Memory Management<a name="javamemorymanagement"></a>

The good news is that it means no more `java.lang.OutOfMemoryError: PermGen` space problems and no need for you to tune and monitor this memory space anymore…not so fast. While this change is invisible by default, we will show you next that you will still need to worry about the class metadata memory footprint. Please also keep in mind that this new feature does not magically eliminate class and classloader memory leaks. You will need to track down these problems using a different approach and by learning the new naming convention.

Set Memory

-Xms<size>        set initial Java heap size
-Xmx<size>        set maximum Java heap size
-Xss<size>        set java thread stack size

### Set JVM to 64 bit
```bash
java -d64 -Xms512m -Xmx4g HelloWorld
```
> Note : -d64: Will enable 64-bit JVM.


### Comment Line Arguments

These are three main categories of Command-Line Argument options:
- **Standard options** :Options that begin with - are Standard options are expected to be accepted by all JVM implementations and are stable between releases (though they can be deprecated).
- **Non-standard options** :Options that begin with -X are non-standard (not guaranteed to be supported on all JVM implementations), and are subject to change without notice in subsequent releases of the Java SDK.
- **Developer options** :Options that begin with -XX are developer options and often have specific system requirements for correct operation and may require privileged access to system configuration parameters; they are not recommended for casual use. These options are also subject to change without notice.

### CMSClassUnloadingEnabled

The standard Oracle/Sun VM look on the world is: Classes are forever. So once loaded, they stay in memory even if no one cares anymore. This usually is no problem since you don't have that many purely "setup" classes (= used once for setup and then never again). So even if they take up 1MB, who cares.

But lately, we have languages like Groovy, that define classes at runtime. Every time you run a script, one (or more) new classes are created and they stay in PermGen forever. If you're running a server, that means you have a memory leak.

If you enable `CMSClassUnloadingEnabled` the GC will sweep PermGen, too, and remove classes which are no longer used.
Groovy creates classes dynamically, but the default Java VM does not GC the PermGen. If you are using Java 6 or later, add
`-XX:+CMSClassUnloadingEnabled -XX:+UseConcMarkSweepGC`. UseConcMarkSweepGC is needed to enable CMSClassUnloadingEnabled.

[blog](https://redfin.engineering/cmsclassunloadingenabled-at-redfin-5a6206499c6b)

### UseConcMarkSweepGC

### MaxMetaSpaceSize

### Excessive GC Time and OutOfMemoryError

The CMS collector throws an OutOfMemoryError if too much time is being spent in garbage collection: if more than 98% of the total time is spent in garbage collection and less than 2% of the heap is recovered, then an OutOfMemoryError is thrown. This feature is designed to prevent applications from running for an extended period of time while making little or no progress because the heap is too small. If necessary, this feature can be disabled by adding the option -XX:-UseGCOverheadLimit to the command line.

The policy is the same as that in the parallel collector, except that time spent performing concurrent collections is not counted toward the 98% time limit. In other words, only collections performed while the application is stopped count toward excessive GC time. Such collections are typically due to a concurrent mode failure or an explicit collection request (for example, a call to System.gc).