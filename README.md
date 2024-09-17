## procstat
tools to stat processes
Purpose: This software traces running status of application and reports any abnormal metrics. It continuously monitors key performance indicators, alerts users the potential issues.

### Copyright
Copyright (c) 2024 Liu Hua Jun
All rights reserved.

### Platform
Platform: Linux
Supported OS: CentOS Stream 8+、Redhat 8+、Fedora 36+.
              Other OS will test later.

### Dependency
1. procstat depends on eBPF and CORE features.
2. libelf is need to run procstat.
3. procstat needs to access internet, it must get authorize from server before working.
4. readelf, cppfilt must be installed in your OS.

### Limitation
Before start this program, please set up below settings.
ulimit -n 65536
ulimit -s 16384

### Usage
Run "sh start.sh -h" to get the usage.

### Configurations:
1. block_stat
    Block io latency stat.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) duration
        >=0, if block io duration >= this value(nanosecond), will report it to log file.
        -1, never report, default value.
    Example:
    "block_stat": {
        "enable": true,
        "duration": 1000000
    }
2. vmscan_stat
    Virtual memory exception stat.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) direct_reclaim_duration
        >=0, if direct reclaim memory duration >= this value(nanosecond), will report it to log file.
        -1, never report.
        default value is 0.
    (3) compact_duration
        >=0, if direct compact memory duration >= this value(nanosecond), will report it to log file.
        -1, never report.
        default value is 0.
    Example:
    "vmscan_stat": {
        "enable": true,
        "direct_reclaim_duration": 1000,
        "compact_duration": 100000
    }
3. schedule_stat
    OS schedule stat. Every 50ms, report off cpu statistics information to log file if exists off cpu events.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) offcpu_duration
        >=0, if off cpu duration >= this value(nanosecond), will report details to log file, contains stacks.
        -1, never report detail information, default value.
    Example:
    "schedule_stat": {
        "enable": true,
        "offcpu_duration": 1000
    }
4. workqueue_stat
    OS workqueue stat. Every 50ms, report workqueue statistics information to log file if exists workqueue events.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) duration
        >=0, if workqueue duration >= this value(nanosecond), will report it to log file.
        -1, never report, default value.
    Example:
    "workqueue_stat": {
        "enable": true,
        "duration": 1000000
    }
5. profile_stat
    Profile a program. Must use command line parameter -p or -t to specify a running process.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) frequency
        >0, profile frequency, how many times to sample in 1 second.
        -1, never sample.
    Example:
    "profile_stat": {
        "enable": true,
        "frequency": 1000
    }
6. proc_info_stat
    Proccess info from /proc/pid/stat. Every 50ms, stat user time, system time, memory used, and so on.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    Example:
    "proc_info_stat": {
        "enable": true
    }
7. cputime_stat
    Every 50ms, stat user cpu time and kernel cpu time.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    Example:
    "cputime_stat": {
        "enable": true
    }
8. lock_stat
    Stat lock info. Every 10s, stat deadlock informations and report to log file if exists.
    Stat lock duration and wait log duration.
    Configuration items:
    (1) enable
        true, enable this stat, default value.
        false, disable this stat.
    (2) lock_duration
        >=0, if lock duration >= this value(nanosecond), will report it to log file.
        -1, never report, default value.
    (3) wait_lock_duration
        >=0, if wait lock duration >= this value(nanosecond), will report it to log file.
        -1, never report, default value.
    Example:
    "lock_stat": {
        "enable": true
        "lock_duration": 1000000,
        "wait_lock_duration": 1000000
    }
9. stl_realloc_stat    
    Stat C++ STL realloc info.    
    Configuration items:    
    (1) enable    
        true, enable this stat, default value.    
        false, disable this stat.    
    (2) vector_realloc_duration    
        >=0, if std::vector realloc's duration >= this value(nanosecond), will report it to log file.    
        -1, never report, default value.    
    (3) unordered_map_realloc_duration    
        >=0, if std::unordered_map realloc's duration >= this value(nanosecond), will report it to log file.    
        -1, never report, default value.    
    (4) unordered_set_realloc_duration    
        >=0, if std::unordered_set realloc's duration >= this value(nanosecond), will report it to log file.    
        -1, never report, default value.    
    (5) vector_realloc_symbol    
        don't need to configure, procstat will configure it automatically.    
    (6) unordered_map_realloc_symbol    
        don't need to configure, procstat will configure it automatically.    
    (7) unordered_set_realloc_symbol    
        don't need to configure, procstat will configure it automatically.    
    Example:    
    "stl_realloc_stat": {    
        "enable": true,    
        "vector_realloc_symbol": "",   
        "vector_realloc_duration": 0,   
        "unordered_map_realloc_symbol": "",   
        "unordered_map_realloc_duration": 0,   
        "unordered_set_realloc_symbol": "",   
        "unordered_set_realloc_duration": 0,   
        "desc": "stl realloc stat"   
    }   
10. gc_stat   
    Stat gc info for golang or jvm program.   
    Configuration items:   
    (1) enable   
        true, enable this stat, default value.   
        false, disable this stat.   
    (2) gc_duration      
        >=0, if gc duration >= this value(nanosecond), will report it to log file.      
        -1, never report, default value.      
    (3) libjvm_path    
        path of libjvm.so, such as "/usr/local/jdk/jre/lib/amd64/server/libjvm.so". If stats program which running on jvm, Option 1. Specify path of libjvm.so. Option 2. Remove this item or set it to "", will search some general paths in Linux environment variable "JAVA_HOME".    
    (4) jvm_gc_symbol    
        symbol, jvm gc symbol is in jvm. If stats program which running on jvm, Option 1. Specify jvm path. Option 2. Remove this item, will use default one.    
        "", never report jvm gc info.    
    Example:    
    "gc_stat": {    
        "enable": true,    
        "gc_duration": 0,    
        "libjvm_path": ""    
    }        
