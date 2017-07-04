STUDENT CONTROLLER
package com.controller;

import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import io.swagger.annotations.ApiResponse;
import io.swagger.annotations.ApiResponses;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import springfox.documentation.swagger2.annotations.EnableSwagger2;

import com.dao.StudentDAO;
import com.domain.Student;
import com.error.Error;
import com.error.Data;
import com.error.MetaData;
import com.error.Response;

@EnableSwagger2
@RestController
public class StudentController {

	@Autowired
	private StudentDAO studentDao;

	@Autowired
	MetaData metaData;

	@Autowired
	Data data;
	@Autowired
	Response response;

	@Autowired
	Error error;

	@ApiOperation(value = "retrieve a student record using GET method", notes = "Returns the student data with student id", response = Response.class)
	@ApiResponses(value = {
		    @ApiResponse(code = 200, message = "Successful retrieval of student details", response = Response.class),
		    @ApiResponse(code = 404, message = "student with given student id does not exist",response = Response.class),
		    @ApiResponse(code = 400, message = "student with student id does not exist",response = Response.class),
		    @ApiResponse(code = 500, message = "Internal Server Error",response = Response.class)})
	@RequestMapping(value = "/students/{id}", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.APPLICATION_JSON_VALUE)
	public ResponseEntity<Response> getStudent(
			@ApiParam(value = "id of student", required = true) @PathVariable("id") int id) {
		List<Student> studentList = null;
		studentList = studentDao.getStudent(id);
		ResponseEntity<Response> responseEntity = null;
		if (studentList.isEmpty()) {
			Error error = new Error();
			error.setCode("TRA2001");
			error.setDescription("Student record not found");
			saveMetaData(false, "Error Occured", "12345");
			saveResponse(null, metaData, error);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.NOT_FOUND);
		} else {
			saveMetaData(true, "Tests loaded", "12345");
			saveData(null, studentList);
			saveResponse(data, metaData, null);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.OK);
		}
		return responseEntity;
	}

	@ApiOperation(value = "retrieve all student records using GET method", notes = "Returns the entire student data", response = Response.class)
	@ApiResponses(value = {
		    @ApiResponse(code = 200, message = "Successful retrieval of student details", response = Response.class),
		    @ApiResponse(code = 404, message = "User with given student id does not exist",response = Response.class),		   
		    @ApiResponse(code = 400, message = "student with student id does not exist",response = Response.class),
		    @ApiResponse(code = 500, message = "Internal Server Error",response = Response.class)})
	@RequestMapping(value = "/students", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.APPLICATION_JSON_VALUE)
	public ResponseEntity<Response> getAllStudents() {
		List<Student> studentList = null;
		studentList = studentDao.getAllStudents();
		ResponseEntity<Response> responseEntity = null;
		if (studentList.isEmpty()) {
			Error error = new Error();
			error.setCode("TRA2001");
			error.setDescription("Student records not found");
			saveMetaData(false, "Error Occured", "12345");
			saveResponse(null, metaData, error);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.NOT_FOUND);
		} else {
			saveMetaData(true, "Tests loaded", "12345");
			saveData(null, studentList);
			saveResponse(data, metaData, null);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.OK);
		}
		return responseEntity;
	}

	@ApiOperation(value = "Save a student record using POST method", notes = "Create student data ", response = Response.class)
	@ApiResponses(value = {
		    @ApiResponse(code = 201, message = "some thing went wrong", response = Response.class),
		    @ApiResponse(code = 404, message = "some thing went wrong", response = Response.class),
		    @ApiResponse(code = 400, message = "Bad Request", response = Response.class),
		    @ApiResponse(code = 500, message = "Internal Server Error", response = Response.class)}
		)
	@RequestMapping(value = "/students", method = RequestMethod.POST, produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.APPLICATION_JSON_VALUE)
	public ResponseEntity<Response> insertStudent(@RequestBody Student student) {
		System.out.println("Inside insert method");
		ResponseEntity<Response> responseEntity = null;
		if (studentDao.checkId(student.getId())) {
			studentDao.insertStudent(student);
			List<Student> studentList = null;
			studentList = studentDao.getStudent(student.getId());
			saveData(null, studentList);
			saveMetaData(true, "Test Created", "14563");
			saveResponse(data, metaData, null);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.CREATED);

		} else {
			Error error = new Error();
			error.setCode("TRA2002");
			error.setDescription("Student id already exists");
			saveMetaData(false, "Error Occured", "14563");
			saveResponse(null, metaData, error);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.CONFLICT);
		}
		return responseEntity;
	}

	@ApiOperation(value = "Delete a student record using GET method", notes = "Delete the student data with student id", response = Response.class)
	@ApiResponses(value = {
		    @ApiResponse(code = 200, message = "Successful deletion  of student ", response = Response.class),
		    @ApiResponse(code = 404, message = "student with student id does not exist",response = Response.class),
		    @ApiResponse(code = 400, message = "student with student id does not exist",response = Response.class),
		    @ApiResponse(code = 500, message = "Internal Server Error",response = Response.class)}
		)
	@RequestMapping(value = "/students/{id}", method = RequestMethod.DELETE, produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.APPLICATION_JSON_VALUE)
	public ResponseEntity<Response> deleteStudent(
			@ApiParam(value = "id of student", required = true) @PathVariable("id") int id) {
		List<Student> studentList = null;
		studentList = studentDao.getStudent(id);
		ResponseEntity<Response> responseEntity = null;
		if (studentDao.checkId(id)) {
			Error error = new Error();
			error.setCode("TRA2003");
			error.setDescription("Student id does not exist");
			saveMetaData(false, "Error Occured", "24541");
			saveData(error, null);
			saveResponse(null, metaData, error);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.NOT_FOUND);
		} else {
			studentDao.deleteStudent(id);
			saveMetaData(true, "Tests Deleted", "24541");
			saveData(null, studentList);
			saveResponse(data, metaData, null);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.OK);
		}
		return responseEntity;

	}

	@ApiOperation(value = "Update a student record using PUT method", notes = "Update the student data with student id", response = Response.class)
	@ApiResponses(value = {
		    @ApiResponse(code = 200, message = "Successful creation  of student ", response = Response.class),
		    @ApiResponse(code = 404, message = "some thing went wrong",response = Response.class),
		    @ApiResponse(code = 400, message = "student with student id does not exist",response = Response.class),
		    @ApiResponse(code = 500, message = "Internal Server Error",response = Response.class)}
		)
	@RequestMapping(value = "/students/{id}", method = RequestMethod.PUT, produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.APPLICATION_JSON_VALUE)
	public ResponseEntity<Response> updateStudent(
			@RequestBody Student student,
			@ApiParam(value = "id of student", required = true) @PathVariable("id") int id) {
		ResponseEntity<Response> responseEntity = null;

		if (studentDao.checkId(id)) {
			Error error = new Error();
			error.setCode("TRA2003");
			error.setDescription("Student id does not exist");
			saveMetaData(false, "Error Occured", "123457");

			saveResponse(null, metaData, error);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.NOT_FOUND);
		} else {
			studentDao.updateStudent(id, student);
			List<Student> studentList = null;
			studentList = studentDao.getStudent(student.getId());
			saveMetaData(true, "Test Updated", "123457");
			saveData(null, studentList);
			saveResponse(data, metaData, null);
			responseEntity = new ResponseEntity<Response>(response,
					HttpStatus.OK);
		}
		return responseEntity;

	}

	private void saveResponse(Data data, MetaData metaData, Error errorDet) {
		response.setData(data);
		response.setMetaData(metaData);
		response.setError(errorDet);
	}

	private void saveData(Error erroDet, List testObj) {
		response.setError(erroDet);
		data.setOutput(testObj);

	}

	private void saveMetaData(boolean success, String description,
			String responseId) {

		metaData.setSuccess(success);
		metaData.setDescription(description);
		metaData.setResponseId(responseId);
	}
}


STUDENT DAO
package com.dao;

import java.util.List;

import javax.sql.DataSource;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.support.JdbcDaoSupport;
import org.springframework.stereotype.Component;

import com.domain.Student;

@Component
public class StudentDAO extends JdbcDaoSupport {

	@Autowired
	public StudentDAO(DataSource dataSource) {
		setDataSource(dataSource);
	}

	/*
	 * @Autowired StudentMapper studentMapper;
	 */

	public List<Student> getStudent(int id) {
		List<Student> student = null;
		String sql = "select * from StudentMaster where id = ?";
		student = getJdbcTemplate().query(sql, new Object[] { id },
				new StudentMapper());
		return student;
	}

	public List<Student> getAllStudents() {
		List<Student> studentList = null;
		String sql = "select * from StudentMaster";
		studentList = getJdbcTemplate().query(sql, new Object[] {},
				new StudentMapper());
		return studentList;

	}

	public boolean checkId(int id) {
		List<Student> student = null;
		String sql = "select * from StudentMaster where id = ?";
		student = getJdbcTemplate().query(sql, new Object[] { id },
				new StudentMapper());
		if (student.isEmpty()) {
			return true;
		} else
			return false;
	}

	public int insertStudent(Student student) {
		String query = "insert into StudentMaster values(?, ?, ?)";
		int result = getJdbcTemplate().update(
				query,
				new Object[] { student.getId(), student.getName(),
						student.getAge() });
		return result;
	}

	public int deleteStudent(int id) {
		String query = "delete from StudentMaster where id = ?";
		int result = getJdbcTemplate().update(query, new Object[] { id });
		return result;
	}

	public int updateStudent(int id, Student student) {
		String query = "update StudentMaster set age = ? where id = ?";
		int result = getJdbcTemplate().update(query,
				new Object[] { student.getAge(), id });
		return result;
	}

}


STUDENT MAPPER

package com.dao;

import java.sql.ResultSet;
import java.sql.SQLException;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Component;

import com.domain.Student;

/*@Component*/
public class StudentMapper implements RowMapper<Student> {
	/*
	 * @Autowired private Student student;
	 */
	public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
		/*
		 * BeanFactory factory = new XmlBeanFactory (new
		 * ClassPathResource("spring-servlet.xml")); Student student = (Student)
		 * factory.getBean("student");
		 */

		Student student = new Student();
		student.setId(rs.getInt(1));
		student.setName(rs.getString(2));
		student.setAge(rs.getInt(3));
		return student;
	}

}

STUDENT

package com.domain;

import io.swagger.annotations.ApiModelProperty;

/*@Component*/
public class Student {
	@ApiModelProperty(position = 1, required = true, value = "id")
	private int id;
	@ApiModelProperty(position = 2, required = true, value = "name")
	private String name;
	@ApiModelProperty(position = 3, required = true, value = "age")
	private int age;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}
}


DATA

package com.error;

import io.swagger.annotations.ApiModelProperty;

import java.util.List;

import com.domain.Student;

public class Data {

	@ApiModelProperty(position = 1, required = true, value = "brief description of the property :output ")
	private List<Student> output;

	public List<Student> getOutput() {
		return output;
	}

	public void setOutput(List<Student> outputList) {
		this.output = outputList;
	}

}


ERROR

package com.error;

import io.swagger.annotations.ApiModelProperty;

import javax.annotation.Generated;
import javax.xml.bind.annotation.XmlRootElement;

@Generated("org.jsonschema2pojo")
@XmlRootElement(name = "Errors")
public class Error {
	@ApiModelProperty(position = 1, required = true, value = "code")
	private String code;
	@ApiModelProperty(position = 2, required = true, value = "description")
	private String description;

	public String getCode() {
		return code;
	}

	public void setCode(String code) {
		this.code = code;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

}


METADATA

package com.error;

import io.swagger.annotations.ApiModelProperty;

public class MetaData {
	@ApiModelProperty(position = 2, required = true, value = "brief description of the property :description ")
	private String description;
	@ApiModelProperty(position = 1, required = true, value = "brief description of the property :responseId ")
	private String responseId;
	@ApiModelProperty(position = 3, required = true, value = "brief description of the property :success ")
	private boolean success;

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public String getResponseId() {
		return responseId;
	}

	public void setResponseId(String responseId) {
		this.responseId = responseId;
	}

	public boolean getSuccess() {
		return success;
	}

	public void setSuccess(boolean success) {
		this.success = success;
	}

}


RESPONSE

package com.error;

import io.swagger.annotations.ApiModelProperty;

public class Response {
	@ApiModelProperty(position = 1, required = true, value = "Test Duration containing numbers in mins")
	private MetaData metaData;
	@ApiModelProperty(position = 2, required = true, value = "Test Duration containing numbers in mins")
	private Data data;
	@ApiModelProperty(position = 3, required = true, value = "Test details")
	private Error error;

	public MetaData getMetaData() {
		return metaData;
	}

	public void setMetaData(MetaData metaData) {
		this.metaData = metaData;
	}

	public Data getData() {
		return data;
	}

	public void setData(Data data) {
		this.data = data;
	}

	public Error getError() {
		return error;
	}

	public void setError(Error error) {
		this.error = error;
	}

}

SPRING-SERVLET
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">



 
	<context:component-scan base-package="com" />
		
	<!-- Initialization for data source -->
   <bean id="dataSource" 
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
      <property name="driverClassName" value="org.apache.derby.jdbc.ClientDriver"/>
      <property name="url" value="jdbc:derby://172.24.18.66:1527/rohnie"/>
      <property name="username" value="user"/>
      <property name="password" value="pwd"/>
   </bean>

   <!--  <bean id="student" class="com.domain.Student">
   </bean> 
		 -->
		 
		
	 <bean id="metaData" class="com.error.MetaData" />
	 <bean id="data" class="com.error.Data" />
	 <bean id="error" class="com.error.Error" />	
	 <bean id="response" class="com.error.Response" />		
	 
	 
	<mvc:annotation-driven />
	<mvc:default-servlet-handler />
	
	</beans>
  
  WEB.XML
  <!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  
   <servlet>
      <servlet-name>spring</servlet-name>
      <servlet-class>
         org.springframework.web.servlet.DispatcherServlet
      </servlet-class>
      <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
      <servlet-name>spring</servlet-name>
      <url-pattern>/</url-pattern>
   </servlet-mapping>
   
   
</web-app>

  
