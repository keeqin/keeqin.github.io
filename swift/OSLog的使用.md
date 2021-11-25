# struct Logger
首先  
import OSLog
创建一个实例:
let logger = Logger()

# 成员方法

## func log(_ message: OSLogMessage)
写入debug等级的log,使用方法:  
logger.log("This is a debug level log")
## func log(level: OSLogType, _ message: OSLogMessage)
写入OSLogType等级的log,等级有degbug,info,default,error,fault,还可以自建等级
使用方法:  
logger.log(level: .error, "This is a error level log")
## log等级方法
logger.notice("This is a notice level log")
logger.debug("This is a debug level log")
logger.trace("This is a trace level log")
logger.info("This is a info level log")
logger.error("This is a error level log")
logger.warning("This is a warning level log")
logger.fault("This is a fault level log")
logger.critical("This is a critical level log")

# 不创建实例直接打印log(不推荐)
os_log("This is a default level log")
