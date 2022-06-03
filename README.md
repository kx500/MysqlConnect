1、安装 Mysql Connect 插件
Install the Mysql Connect plugin

https://www.unrealengine.com/marketplace/zh-CN/product/c15f4e325ba04d5698395414af94644f
1
2、使用插件
Use plugins


![未标题-111](https://user-images.githubusercontent.com/41780542/168425467-505c733f-cd98-454e-a0f5-64ae4e8b13ea.png)
![未标题-1](https://user-images.githubusercontent.com/41780542/168425469-c2960909-2fe9-4e8b-9d7b-a64e445e63fb.png)


Features:（产品特点）

Support local MYSQL server and cloud database connection (eg: Azure Database for MySQL)
支持本地MYSQL服务器和云数据库连接（例如：Azure Database for MySQL）

Support C++ or pure blueprint calls
支持 C++ 或纯蓝图调用

Synchronous and asynchronous support
同步和异步支持

Support MYSQL 5.x.x - 8.x.x series
支持MYSQL 5.x.x - 8.x.x 系列

used in C++（在 C++ 中使用）


	Mysql = UMysqlConnectSubsystem::Get();
 
	Results = NewObject<UMysqlResult>();

	/////////////////////////// synchronous connection example ///////////////////////////////

	Mysql->Connect("127.0.0.1", 3306, "username", "pw", "TableName", Results);
	if (!Results->IsSucceed)
	{
		UE_LOG(LogTemp, Warning, TEXT("MYSQL: ConnectError: %s"), *Results->Msg);
		return;
	}

	//Get the total number of rows
	Mysql->Query("SELECT count(1) FROM `testtable`", Results);
	UE_LOG(LogTemp, Warning, TEXT("MYSQL: Count: IsSucceed = %d, msg = %s, count = %d"), Results->IsSucceed, *Results->Msg, Results->getCount());

	//get row
	Mysql->Query("SELECT * FROM `testtable`", Results);
	UE_LOG(LogTemp, Warning, TEXT("MYSQL: rows: IsSucceed = %d, msg = %s, Num = %d"), Results->IsSucceed, *Results->Msg, Results->getRows().Num());


/////////////////////////// Asynchronous connection example ///////////////////////////////

	Mysql = UMysqlConnectSubsystem::Get();
 
	Results = NewObject<UMysqlResult>();
	
	const FLatentActionInfo LatentInfo(0, FMath::Rand(), TEXT("ConnectCallback"), this);
	Mysql->AConnect("127.0.0.1", 3306, "username", "pw", "TableName", Results, this, LatentInfo);
 
	//Asynchronous callback function
    void APluginProjectGameModeBase::ConnectCallback()
    {
        if (!Results->IsSucceed)
        {
            UE_LOG(LogTemp, Warning, TEXT("MYSQL: ConnectError: %s"), *Results->Msg);
            return;
        }
    
        //Get the total number of rows
        const FLatentActionInfo LatentInfo(0, FMath::Rand(), TEXT("fetch_count_Callback"), this);
        Mysql->AQuery("SELECT count(1) FROM `testtable`", Results, this, LatentInfo);
    }
    
    //Asynchronous callback function
    void APluginProjectGameModeBase::fetch_count_Callback()
    {
        UE_LOG(LogTemp, Warning, TEXT("MYSQL: Count: IsSucceed = %d, msg = %s, count = %d"), Results->IsSucceed, *Results->Msg, Results->getCount());
    
        //get row
        const FLatentActionInfo LatentInfo(0, FMath::Rand(), TEXT("fetch_rows_Callback"), this);
        Mysql->AQuery("SELECT * FROM `testtable`", Results, this, LatentInfo);
    }
    
    //Asynchronous callback function
    void APluginProjectGameModeBase::fetch_rows_Callback()
    {
        UE_LOG(LogTemp, Warning, TEXT("MYSQL: rows: IsSucceed = %d, msg = %s, Num = %d"), Results->IsSucceed, *Results->Msg, Results->getRows().Num());
    }
