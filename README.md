# snake
贪吃蛇数据库部分
#action部分
package com.snake.action;

import java.util.ArrayList;
import java.util.Map;

import javax.annotation.Resource;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.servlet.http.HttpServletRequest;

import org.apache.struts2.interceptor.SessionAware;  
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;

import com.snake.dao.UserDao;
import com.snake.model.User;
import com.opensymphony.xwork2.ActionSupport;


@Entity
@Controller @Scope("prototype")

public class UserAction<UserDao> extends ActionSupport implements SessionAware{
	


	/**
	 * 
	 */
	@Id
	@GeneratedValue
	private static final long serialVersionUID = 1L;

	/*业务层对象*/
    @Resource UserDao UserDao;
    
    private User user;
    
    //这两个成员变量是用来做登录拦截的，记得添加setter和getter
    
	private Map<String,Object> session;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}
	
	public Map<String, Object> getSession() {
		return session;
	}

	public void setSession(Map<String, Object> session) {
		this.session = session;
	}

	
	
	private String errMessage;
	
	public String getErrMessage() {
		return errMessage;
	}

	public void setErrMessage(String errMessage) {
		this.errMessage = errMessage;
	}
	
	/*
	public String reg() throws Exception{
		customerDao.AddCustomer(customer);
		session.put("curCustomer", customer);
		return "show_view";
		
	}*/
	
    //注册，并在session中加入用户名
	public String reg() throws Exception{
		//UserDao.AddUser(user);
		session.put("user", user);
		return "show_view";

	}
    
	/* 验证用户登录 */
	/*public String login() {
		Customer db_customer = (Customer)customerDao.QueryCustomerInfo(customer.getName()).get(0);
		if(db_customer == null) { 
			
			this.errMessage = " 账号不存在 ";
			System.out.print(this.errMessage);
			return INPUT;
			
		} else if( !db_customer.getPassword().equals(customer.getPassword())) {
			
			this.errMessage = " 密码不正确! ";
			System.out.print(this.errMessage);
			return INPUT;
			
		}else{
			return "show_view";
			
		}	*/	

	
	/* 验证用户登录 */
	public String login() {
			
	Map<String, Object> listUser = null;
	Object db_user = null;
	//ArrayList<User> listUser = UserDao.QueryUserInfo(user.getName());
	if(listUser.size()==0) { 
		this.errMessage = " 账号不存在 ";
		System.out.print(this.errMessage);
		return "input";
		} 
		else if(!((Object) db_user).equals(user.getPassword(errMessage))){
		this.errMessage = " 密码不正确! ";
		System.out.print(this.errMessage);
		return "input";
		}
		else{
			session.put("user", db_user);
			return "success";
		}
	}
}#dao部分
package com.snake.dao;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

import javax.annotation.Resource;

import org.hibernate.Query;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.snake.model.User;


@Service @Transactional
public class UserDao {
	@Resource SessionFactory factory;
	
	 /*添加User信息*/
    public void AddCustomer(User user) throws Exception {
    	Session s = factory.getCurrentSession();
    	s.save(user);
    }
    
    /*删除Customer信息*/
    public void DeleteUSer (Integer customerId) throws Exception {
        Session s = factory.getCurrentSession(); 
        Serializable userid = null;
		Object user = s.load(User.class, userid);
        s.delete(user);
    }
    
    /*更新Customer信息*/
    public void UpdateUser(User user) throws Exception {
        Session s = factory.getCurrentSession();
        s.update(user);
    }
    
    /*查询所有Customer信息*/
    public ArrayList<User> QueryAllCustomer() {
        Session s = factory.getCurrentSession();
        String hql = "From User";
        Query q = s.createQuery(hql);
        List userList = q.list();
        return (ArrayList<User>) userList;
    }

    /*根据主键获取对象*/
    public User GetUserById(Integer userid) {
        Session s = factory.getCurrentSession();
        User user = (User)s.get(User.class, userid);
        return user;
    }
    
    /*根据查询条件查询*/
    public ArrayList<User> QueryCustomerInfo(String name) {
    	
    	Session s = factory.getCurrentSession();
    	List userList;
    	String hql = "From User user where 1=1";
    	if(!name.equals("")){ 
    		
    		hql = hql + " and user.name like '%" + name + "%'";
	    	Query q = s.createQuery(hql);
	    	userList = q.list();
	    	
    	}else{
    		
    	    userList =null;	
    	
    	}
    	return (ArrayList<User>) userList;
    }

}
#model部分
package com.snake.model;

import java.util.HashSet;

import java.util.Set;
import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.OneToMany;
import javax.persistence.Table;
import org.hibernate.annotations.GenericGenerator;



/**
 * Customer entity. @author MyEclipse Persistence Tools
 */
@Entity
@Table(name = "t_user", catalog = "snake")
public class User implements java.io.Serializable {

	// Fields

	private String name;
	private Integer grade;
	private Integer ranking;
	private Integer userid;
	private String password;

	// User

	/** default constructor */
	public User() {
	}

	/** minimal constructor */
	public User(String name) {
		this.name = name;
	}

	/** full constructor */
	public User(String name, int grade, int ranking,String password) {
		this.name = name;
		this.grade = grade;
		this.ranking=ranking;
		this.password=password;
	}

	// Property accessors
	@GenericGenerator(name = "generator", strategy = "increment")
	@Id
	@GeneratedValue(generator = "generator")
	@Column(name = "customerid", unique = true, nullable = false)
	public Integer getCustomerid() {
		return this.userid;
	}

	public void setCustomerid(Integer userid) {
		this.userid = userid;
	}

	@Column(name = "name", nullable = false, length = 20)
	public String getName() {
		return this.name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Column(name = "grade", length = 40)
	public int getGrade() {
		return this.grade;
	}

	public void setGrade(int grade) {
		this.grade = grade;
	}

	@Column(name = "ranking", length = 16)
	public int getRanking() {
		return this.ranking;
	}

	public void setRanking(int ranking) {
		this.ranking = ranking;
	}

	public String getPassword(String password) {
		return this.password;
	}
	public void setPassword(String password) {
		this.password=password;
	}

}

#util部分
package com.snake.util;

import java.util.ArrayList;

import java.util.List;
import java.util.Map;
import java.io.ObjectOutputStream;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.servlet.http.HttpServletRequest;

import org.apache.struts2.ServletActionContext;

import com.snake.model.User;
import com.opensymphony.xwork2.ActionInvocation;
import com.opensymphony.xwork2.interceptor.AbstractInterceptor;

@Entity
public class LoginInterceptors extends AbstractInterceptor{

	 
	   @Id
	@GeneratedValue
	private static final long serialVersionUID = 1L;

	   private String sessionName; //用来传递当前用户的信息

	   private String excludeName; //例外的action，也就是凡是例外的action就不需要拦截

	   private List <String> list;

	   /*
	    * 因为在配置文件的参数excludeName中，例外的action可能是若干个，中间用逗号间隔，所以在取值时要把excludeName分解成单独的
	   一个个action的名字，放在一个list中，strlsit方法用来分解这些action的名字
	                 假如逗号前后有空格，则使用trim()方法去除这些空格
	   */
	   public List<String>  strlsit(String str){

	     String[] s = str.split(",");

	     List <String> list = new ArrayList <String>();

	     for(String ss : s){

	        list.add(ss.trim());

	     }

	     return list;

	   }

	   @Override

	   public void init() {

	      list = strlsit(excludeName);

	   }

	   @Override

	   public String intercept(ActionInvocation invocation) throws Exception {

	     
		 System.out.println("--------进入拦截器--------");  //此语句可以用来验证是否能进入intercept方法
		 String actionName = invocation.getProxy().getActionName();   //得到跳转的action的名字
		 Map <String,Object>  session = invocation.getInvocationContext().getSession();  //得到当前session

	     if(list.contains(actionName)){   //如果请求合法，也就是进登录或注册的action，则不拦截
	        
	    	System.out.println(actionName + "没有被拦截");
	        return invocation.invoke();     //就是通知struts2继续处理以后的事儿，也就是不加拦截器时该执行的那个action

	     }else {   //如果请求不合法，也就是需要被拦截

	        //查看session
	    	System.out.println(actionName + "被拦截了");

	        
	        
	        //得到当前用户（当前用户在login方法中已经被放入session中，见UserAction中的login方法）
	        User user = (User) session.get(sessionName);   
	        
	           if(user==null){   //如果user不存在，就说明登录不成功，还转回login
	        	     // 获取HttpServletRequest对象  
	                 HttpServletRequest req = ServletActionContext.getRequest();  

	                 // 获取此请求的地址 ，也就是获取拦截前要跳转的地址，进行跳转
	                 String path = req.getRequestURI().replaceAll("/snakeProject", "");
	                 System.out.println("path:" + path);
	        
	                 // 存入session，这个参数在struts.xml中会作为参数出现  
	                 session.put("prePage", path);  
	        	     return "login";
	           }
	           else {                //如果user存在，则登录成功
	        	
	                 return invocation.invoke();    //通知struts2跳转到action中

	          }

	     }

	   }

	   public String getSessionName() {

	     return sessionName;

	   }

	   public void setSessionName(String sessionName) {

	     this.sessionName = sessionName;

	   }

	   public String getExcludeName() {

	     return excludeName;

	   }

	   public void setExcludeName(String excludeName) {

	     this.excludeName = excludeName;

	   }

	   public List<String> getList() {

	     return list;

	   }

	   public void setList(List<String> list) {

	     this.list = list;

	   }


	}

#数据库(snake)
create table t_user (name VARCHAR(20), grade int(10),
       ranking int(10));

