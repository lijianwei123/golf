# golf
动态应用级防火墙，支持白名单，黑名单
# <center>哈哈，因为一些原因，只能先上几张架构图</center>

## 架构图
![架构图](http://chuantu.biz/t6/167/1512461736x-1404764430.png)

### 说明
- ELK负责nginx访问日志收集，对golf console提供查询服务（如最近一分钟访问最多的ip列表、某个访问页面的日志查询)
- Golf lua 负责具体的访问控制, 对外提供更新ip列表的rest api(仅限ecs内网访问)， 数据具体存储在resty lrucache中，当访问的ip在黑名单中，则返回403
- Golf Console 负责nginx应用添加(日志查询接口、更新ip列表的rest url)、黑名单维护

### 配置使用
- 安装openresty
- 修改nginx配置文件
	- 修改http级配置
	
			http
			{
    			#黑名单内存大小
    			lua_shared_dict     test.com 10m;
    			include             golf-lua/http.conf;
			}
			
		
	- 修改server级配置
			
			server
			{
    			
    			listen              80;
    			include             golf-lua/server.conf;
    		}
    	
###  同时提供外部rpc接口
	

		public interface DenyIpApi
		{
		    /**
		     * 黑名单
		     * @param host
		     * @return
		     */
		    @GsfCache
		    PlainResult<List<String>> blackList(String host);

		    /**
		     * 添加黑名单
		     * @param host
		     * @param ipList
		     * @return
		     */
		    PlainResult<Boolean> addIp(String host, List<String> ipList);

		    /**
		     * 删除黑名单
		     * @param host
		     * @param ip
		     * @return
		     */
		    PlainResult<Boolean> delIp(String host, String ip);
		}
	
### 控制台
![控制台](http://chuantu.biz/t6/167/1512462578x-1404764430.png)
