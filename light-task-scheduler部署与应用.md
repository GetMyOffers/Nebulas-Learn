### 1. 数据库准备
执行[light-task-scheduler 数据库脚本](https://github.com/ltsopensource/light-task-scheduler/blob/master/lts-admin/src/main/resources/sql/mysql/lts_admin_node_onoffline_log.sql)

### 2. 构建项目
运行项目根目录[light-task-scheduler](https://github.com/ltsopensource/light-task-scheduler)下的构建脚本：
```shell
sh build.sh
```
会在根目录下生成dist/lts-1.7.1-SNAPSHOT-bin.zip文件，解压

### 3. 配置，启动JobTracker
- 修改配置文件 
lts-1.7.1-SNAPSHOT-bin/conf/zoo目录下的：jobtracker.cfg 、 lts-monitor.cfg ,主要修改zookeeper、mysql的配置信息。

- 运行JobTracker
```shell
cd bin/ #进入lts-1.7.1-SNAPSHOT-bin/bin
sh jobtracker.sh zoo start  #启动JobTracker

##启动成功结果##
Starting LTS JOB_TRACKER [zoo] ... 
STARTED
```
第一次成功启动后，会在lts-1.7.1-SNAPSHOT-bin目录下，生成logs和pid目录。

其中pid目录生成JobTracker的进程PID文件jobtracker-zoo.pid，而logs目录生成JokTracker日志输出文件jobtracker-zoo.out。

### 4. 配置启动Lts-Admin
- 修改配置文件 
lts-1.7.1-SNAPSHOT-bin/conf目录下的：lts-admin.cfg 、 lts-monitor.cfg ，主要修改zookeeper、mysql的配置信息。

- 运行LTS-Admin后台管理Web
```shell
cd bin/ #进入lts-1.7.1-SNAPSHOT-bin/bin
sh lts-admin.sh start  #启动LTS-Admin

##启动成功结果##
Starting LTS LTS-Admin ... 
STARTED
```
启动成功后，会在logs和pid目录中生成两个文件，logs下生成lts-admin.out，此为LTS-Admin后台网站打印出来的日志(日志中输出相关web站点的信息，比如访问地址)。pid下生成lts-admin.pid，LTS-Admin的进程PID。 

### 5.访问LTS-Admin后台管理Web

http://127.0.0.1:8081/index.htm

### 6. 在项目中配置TaskTracker
在自己的项目中配置TaskTracker

#### - pom.xml记得依赖lts
```xml
  <dependency>
      <groupId>com.github.ltsopensource</groupId>
      <artifactId>lts</artifactId>
      <version>${lts.version}</version>
  </dependency>
```

#### - 首先定义一个TaskTrackerEntry
```java
@Component
public class TaskTrackerEntry {

    static {
        System.out.println("TaskTrackerEntry初始化开始....");
        final TaskTracker taskTracker = new TaskTrackerBuilder()
                .setPropertiesConfigure("lts.properties")
                .build();
        System.out.println("TaskTrackerEntry初始化成功！");


        taskTracker.start();

        Runtime.getRuntime().addShutdownHook(new Thread(new Runnable() {
            @Override
            public void run() {
                taskTracker.stop();
            }
        }));
    }

}
```

#### - 然后定义一个Task分发器 MultiTaskRunnerDispatcher
其作用就是Task被接受以后由它分发给对应类型的JobRunner，也就是所谓的TaskResolver
```java
@JobRunner4TaskTracker
public class MultiTaskRunnerDispatcher implements JobRunner, ApplicationContextAware {
    @Autowired
    private static final ConcurrentHashMap<String, JobRunner> JOB_RUNNER_MAP = new ConcurrentHashMap<String, JobRunner>();

    // @Qualifier(JobRunnerTypes.neb)
    // private static NebJobRunner NEB_JOB_RUNNER;
    // @Autowired
    // MultiTaskRunnerDispatcher(NebJobRunner NEB_JOB_RUNNER) {
    //     this.NEB_JOB_RUNNER = NEB_JOB_RUNNER;
    //     JOB_RUNNER_MAP.put("neb", NEB_JOB_RUNNER);
    // }

    @Override
    public Result run(JobContext jobContext) throws Throwable {
        String type = jobContext.getJob().getParam("type");
        JobRunner runner = JOB_RUNNER_MAP.get(type);
        if(null == runner){
            //throw new RuntimeException("未找到相应的任务执行者, jobKey=" + jobKey);
            return new Result(Action.EXECUTE_FAILED, "未找到相应的任务执行者, JobRunnerTypes=" + type);
        }
        return runner.run(jobContext);
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {

        Map<String, JobRunner> map = applicationContext.getBeansOfType(JobRunner.class);
        for (String type : map.keySet()) {
            JOB_RUNNER_MAP.put(type, map.get(type));
        }
    }
}
```

#### - 定义特定类型的JobRunner
```java
@Component("fetchCoinMarket")
public class FetchCoinMarketJobRunner implements JobRunner {

    private static final Logger LOGGER = LoggerFactory.getLogger(FetchCoinMarketJobRunner.class);
    @Autowired
    @Qualifier("fetchCoinMarketJob")
    private IScheduleHandler fetchCoinMarketJob;


    @Override
    public Result run(JobContext jobContext) throws Throwable {
        try {

//            BizLogger bizLogger = LtsLoggerFactory.getBizLogger();
            BizLogger bizLogger = jobContext.getBizLogger();

            // TODO 业务逻辑
            fetchCoinMarketJob.run();

            LOGGER.info("执行：fetchCoinMarketJob...." + jobContext);
            // 会发送到 LTS (JobTracker上)
            bizLogger.info("fetchCoinMarketJob 执行成功!");

        } catch (Exception e) {
            LOGGER.info("Run fetchCoinMarketJob failed!", e);
            return new Result(Action.EXECUTE_FAILED, e.getMessage());
        }
        return new Result(Action.EXECUTE_SUCCESS, "fetchCoinMarketJob 执行成功!");
    }
}
```

***Note：其中fetchCoinMarketJob可以单独写任务逻辑***


































