---
layout: post
title: Java使用代理和interface完成初级架构
description: 公司Freamwork的提供的lib封装的精髓，怎样把事件业务与java api底层实现相关联。
category: blog
---

吐槽之公司Freamwork Team 对Java端封装，是使用jar的编程人员对Java API底层的透明，同时提供对公司业务interface的迎合。<br/>
这一切的基本点就是使用代理模式和对各种业务的抽象。在代理类继承业务抽象interface,具体被代理对象为底层实现Object。

下面就来说有Jackson,Restful,SOAP,TibCOEMS,JPA.的封装与实现。
Jackson一个JSON转Java object,Java object转JSON的jar包，相信大家都比较熟悉。而且项目中可能都在用。因为JSON数据在AJAX和
webservice传输的时候都可能会用，传输数据量相对xml较小。所以项目中不免会有很多需要有的Jackson的地方。
使用Jackson在各种class和json字符串直接的转换还是不怎么方便，这个时候就产生了我们自己的期望的业务处理接口。

public interface DataTransform<T>{
	public T jsonToObject(String jsonData);
    public String objectToJson(T obj);
}

具体实现类：

public class  JacksonProxy<T> implements DataTransform<T>{
    private ObjectMapper objectMapper = new ObjectMapper();//被代理对象
    public T jsonToObject(String jsonData){
       //使用Jackson object mapper具体实现 writeValueAsString
    }
    public String objectToJson(T obj)｛
       //使用Jackson object mapper具体实现  readValue
    ｝
}


/**restful同时实现client resource和 server resource**/ 
public interface ClientResource<T>{ //T为rest data transform object.
	public T get(int id){

    }
    public boolen update(int id,String obj){

    }
    ....//delete,getall
}

public class ClientResource implements ClientResource<T>{
	private RESTServiceProxy<T> proxy;
    public T get(id){
      //service 建立service之间的连接，构建path.和请求方式GET
      getProxy(class,path).get(id);
    }

    private RESTServiceProxy<T> getProxy(Class clazz, Path path){
         proxy = new RESTServiceProxy<T>(clazz, path);
    }
}

server端

public   class ServerResource implements ClientResource<T>{
	@GET
	@Path("{id}")
    public Response get(@Context HttpHeaders httpheader, @PathParam("id") String id)
      process(id,"TYPE.GET");
      return obj;
    }
   
    public void  process(params,type){//相当于一个分配器统一处理各种类型（GET,POST,PUT,DELETE,HEADER）的请求
        if(type == "TYPE.GET"){
        	get(key);
        }else if(type == "TYPE.PUT"){//当然用switch也可以把执行TYPE弄成枚举

        }
    }
    
    public T get(String key){//用于子类实现

    }
}


/**SOAP**/  
实现对PortType代理服务器端，当然客服端也可以实现wsimport生成的client代码进行代理。用JAXB进行XML String到
Object的转换，String是因为网络传输没用序列化，是传输XML的String，这样方便验证。
@WebService(name = "PortType", targetNamespace = "edu.one")
@HandlerChain(file = "PortType_handler.xml")
public interface PortType{
	@WebMethod(action = "edu.one/findData")
    @WebResult(name = "result", targetNamespace = "edu.one/types/")
    @RequestWrapper(localName = "findData", targetNamespace = "edu.one/types/", className = "edu.one.soap.FindData")
    @ResponseWrapper(localName = "findDataResponse", targetNamespace = "edu.one/types/", className = "edu.one.soap.FindDataResponse")
}

public class PortTypeImpl implements PortType{
	private DataService dataService = new DataService();
}


/**TibCOEMS**/
EMS server在整个SOA架构中器中心枢纽作用。sender和borker就是发布者和订阅者之间的关系。
这个一样封装对初始话JNDI context的管理，destention 目录书的查找管理。

/**JPA**/
可以对unitEntityManger的代理。


这样写的好处是我们直接调用调用业务实现对象就可以了，而不用关心具体的底层实现。而且Spirng中resource的读取同样是使用的
这种封装机制。
写的乱，相当于自己的笔记吧。哈哈
