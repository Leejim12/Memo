# Mission2
## 요약
```
H2 Database에 접속. Spring boot와 연동하여 CRUD 작업.
```

## 전체 진행도

1. MemberService에서 MemberController에서 컨트롤 하기 위한 메소드들 구현.
* MemberService 객체 생성 시, 자동으로 MemberDAO 생성
  - MemberDAO("org.h2.Driver", "jdbc:h2:tcp://localhost/~/test", "sa", "")
  - MemberDAO는 ConnectH2를 상속받음 --> MemberDAO의 생성자 --> super(drv,url,id,pw)
  - ConnectH2는 Connect를 상속받음. ---> super(driver,url,id,pwd)
  - Connect(driver,url,id,pwd)
    - getConnection이 true를 반환하면, 연결 성공 보여줌.
    - false일 경우, 연결 실패 보여줌
  - getConnection(driver,url,id,pwd)
  ```
      boolean getConnection(String driver, String url, String id, String pwd) {
        System.out.println("[driver]" + driver);
        System.out.println("[url   ]" + url);
        System.out.println("[id    ]" + id);
        System.out.println("[pwd   ]" + pwd);
        try {
            // JDBC 드라이버 로드
            Class.forName(driver);  

            // DB에 연결
            con = DriverManager.getConnection(url, id, pwd);

            return true;
        }
        catch (Exception e) {            
            e.printStackTrace();
        }
        return false;
    }
  ```
2. MemberService 기반으로, MemberController에서 특정 매핑 시 구현 사항 지정.




## 전체 코드
### Connector (Package)
> DB에 연결하기 위한 패키지
* Connect.java
```
package com.mysite.connector;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;
import javax.servlet.ServletContext;

public class Connect {
    public Connection con;
    public Statement stmt;  
    public PreparedStatement psmt;  
    public ResultSet rs;

    // 기본 생성자
    public Connect() {}

    // 두 번째 생성자 (얘 사용)
    public Connect(String driver, String url, String id, String pwd) {
    	if (getConnection(driver, url, id, pwd)) {
            System.out.println("[JDBConnect]DB 연결 성공(인수 생성자 1)");
        } else {
        	System.out.println("[JDBConnect]DB 연결 실패(인수 생성자 1)");
        }
    }
    
    public Connect(ServletContext application, String param) {
        String driver = application.getInitParameter(param + "Driver"); 
        String url = application.getInitParameter(param + "URL");
        String id = application.getInitParameter(param + "Id");
        String pwd = application.getInitParameter(param + "Pwd");

    	if (getConnection(driver, url, id, pwd)) {
            System.out.println("[JDBConnect]DB 연결 성공(인수 생성자 2)");
        } else {
        	System.out.println("[JDBConnect]DB 연결 실패(인수 생성자 2)");
        }
   	}

    // 연결 해제(자원 반납)
    public void close() { 
        try {            
            if (rs != null) rs.close(); 
            if (stmt != null) stmt.close();
            if (psmt != null) psmt.close();
            if (con != null) con.close(); 

            System.out.println("[JDBConnect]JDBC 자원 해제");
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }

    boolean getConnection(String driver, String url, String id, String pwd) {

        System.out.println("[driver]" + driver);
        System.out.println("[url   ]" + url);
        System.out.println("[id    ]" + id);
        System.out.println("[pwd   ]" + pwd);
    	
        try {
            // JDBC 드라이버 로드
            Class.forName(driver);  

            // DB에 연결
            con = DriverManager.getConnection(url, id, pwd);

            return true;
        }
        catch (Exception e) {            
            e.printStackTrace();
        }
        return false;
    }    
}
```
* ConnectH2.java
```
package com.mysite.connector;
import javax.servlet.ServletContext;
public class ConnectH2 extends Connect {
    // 기본 생성자
    public ConnectH2() {
        try {
        	String driver = "org.h2.Driver";
        	String url = "jdbc:h2:tcp://localhost/~/test";  
        	String id = "sa";
        	String pwd = "";
        	if (getConnection(driver, url, id, pwd))	System.out.println("[JDBConnectH2]DB 연결 성공(기본 생성자)");
	        else										System.out.println("[JDBConnectH2]DB 연결 실패(기본 생성자)");
        }
        catch (Exception e) {            
            e.printStackTrace();
        }
    }

    // 두 번째 생성자
    public ConnectH2(String driver, String url, String id, String pwd) {
    	super(driver, url, id, pwd);
    }

    // 세 번째 생성자
    public ConnectH2(ServletContext application) {
    	super(application, "H2");
    }
}
```

### Domain (Package)

* MemberDAO.java
```
package com.mysite.domain;

import java.util.ArrayList;
import java.util.List;

import com.mysite.connector.ConnectH2;

public class MemberDAO extends ConnectH2 {
	String driver = "org.h2.Driver";
	String url = "jdbc:h2:tcp://localhost/~/test";  
	String id = "sa";
	String pwd = "";
    // 명시한 데이터베이스로의 연결이 완료된 MemberDAO 객체를 생성합니다.
    public MemberDAO(String drv, String url, String id, String pw) {
        super(drv, url, id, pw);
    }

    public List<MemberVO> getMemberVO() {
    	List<MemberVO> ms = new ArrayList<MemberVO>();
    	String query = "SELECT * FROM member";
    	
        try {
            // 쿼리 실행
            psmt = con.prepareStatement(query); // 동적 쿼리문 준비 // con : 부모로부터 상속받은것.
            rs = psmt.executeQuery();  // 쿼리문 실행
            System.out.println(rs);
            while (rs.next()) {  // 결과를 순화하며...
                // 한 행(게시물 하나)의 내용을 DTO에 저장
            	MemberVO dto = new MemberVO();
            	dto.setId(rs.getString("id"));
            	dto.setPass(rs.getString("pass"));
            	dto.setName(rs.getString("name"));
            	dto.setRegidate(rs.getString("regidate"));
                ms.add(dto);  // 결과 목록에 저장
            }
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        return ms;
    }
    
    // 명시한 아이디/패스워드와 일치하는 회원 정보를 반환합니다.
    public MemberVO getMemberVO(String uid, String upass) {
        MemberVO dto = new MemberVO();  // 회원 정보 DTO 객체 생성
        System.out.println(uid + "," + upass);
        String query = "SELECT * FROM member WHERE id=? AND pass=?";  // 쿼리문 템플릿
        
        try {
            // 쿼리 실행
            psmt = con.prepareStatement(query); // 동적 쿼리문 준비 // con : 부모로부터 상속받은것.
            psmt.setString(1, uid);    // 쿼리문의 첫 번째 인파라미터에 값 설정
            psmt.setString(2, upass);  // 쿼리문의 두 번째 인파라미터에 값 설정
            rs = psmt.executeQuery();  // 쿼리문 실행

            // 결과 처리
            if (rs.next()) {
                // 쿼리 결과로 얻은 회원 정보를 DTO 객체에 저장
                dto.setId(rs.getString("id"));
                dto.setPass(rs.getString("pass"));
                dto.setName(rs.getString(3));
                dto.setRegidate(rs.getString(4));
            }
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        return dto;  // DTO 객체 반환
    }
    
    
    public int insertWrite(MemberVO vo) {
    	int result = 0;
    	try {
    		String query="INSERT INTO MEMBER("
    				+ "id,pass,name)"
    				+ " values("
    				+ "?,?,?)";
    		psmt = con.prepareStatement(query);
    		psmt.setString(1, String.valueOf(vo.getId()));
    		psmt.setString(2, vo.getPass());
    		psmt.setString(3, vo.getName());
    		result = psmt.executeUpdate();
    		
    	}catch(Exception e) {
    		System.out.println("insert 오류");
    		
    		e.printStackTrace();
    	}
    	return result;
    }
    public int updateMember(MemberVO vo) {
    	int result = 0;
    	try {
    		String query="UPDATE MEMBER SET "
    				+ "NAME = ?,"
    				+ "PASS = ?"
    				+ "WHERE ID = ?";
    		psmt = con.prepareStatement(query);
    		psmt.setString(3, String.valueOf(vo.getId()));
    		psmt.setString(2, vo.getPass());
    		psmt.setString(1, vo.getName());
    		result = psmt.executeUpdate();
    		
    	}catch(Exception e) {
    		System.out.println("UPDATE 오류");
    		
    		e.printStackTrace();
    	}
    	return result;
    }
    public int DeleteMember(MemberVO vo) {
    	int result = 0;
    	try {
    		String query="DELETE FROM MEMBER WHERE"
    				+ " ID = ?";
    		psmt = con.prepareStatement(query);
    		psmt.setString(1, String.valueOf(vo.getId()));
    		result = psmt.executeUpdate();
    		
    	}catch(Exception e) {
    		System.out.println("DELETE 오류");
    		e.printStackTrace();
    	}
    	return result;
    }
}
```
* MemberVO.java
```
package com.mysite.domain;

import java.util.Date;
import java.util.HashMap;

public class MemberVO {
	private String id;
	private String pass;
	private String name;
	private String regidate;
	
	
	@Override
	public String toString() {
		return "MemberVO [id=" + id + ", pass=" + pass + ", name=" + name + ", regidate=" + regidate + "]";
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getPass() {
		return pass;
	}
	public void setPass(String pass) {
		this.pass = pass;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getRegidate() {
		return regidate;
	}
	public void setRegidate(String regidate) {
		this.regidate = regidate;
	}
}
```

### Service (Package)
* MemberService.java
```
package com.mysite.service;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import com.mysite.domain.MemberDAO;
import com.mysite.domain.MemberVO;

public class MemberService {

	private MemberDAO memberDAO;
	
	public MemberService() {
		memberDAO  = new MemberDAO("org.h2.Driver", "jdbc:h2:tcp://localhost/~/test", "sa", "");
	}
	
	public List<MemberVO> getMembers(){
		return memberDAO.getMemberVO();
	}
	
	//id 찾기 -> get 
	public MemberVO getMember(String id){
		List<MemberVO> memberList = memberDAO.getMemberVO();
		for(MemberVO m : memberList) {
			if(m.getId().equals(id))return memberDAO.getMemberVO(m.getId(), m.getPass());
		}
		return null;
	}
	
	public MemberVO addMember(MemberVO memberVO){
		List<MemberVO> memberList = memberDAO.getMemberVO();
		memberVO.setRegidate(String.valueOf(new Date()));
		memberVO.setId(String.valueOf(memberList.size()+1));
		memberList.add(memberVO);
		memberDAO.insertWrite(memberVO);
		return memberVO;
	}
	
	
	public MemberVO updateMembers(MemberVO memberVO){
		List<MemberVO> memberList = memberDAO.getMemberVO();
		for(int i = 1;i<=memberList.size()-1;i++) {
			if(memberList.get(i).getId() == memberVO.getId()) {
				memberList.set(i, memberVO);return memberVO;
			}
		}
		memberList.add(memberVO);
		memberDAO.updateMember(memberVO);
		return memberVO;
	}
	
	public MemberVO removeMember(String id){
		List<MemberVO> memberList = memberDAO.getMemberVO();
		for(MemberVO m : memberList) {
			if(m.getId().equals(id)) {
				memberList.remove(m);
				memberDAO.DeleteMember(m);
				return m;
			}
		}
		return null;
	}	
}
```

### Controller
* MemberController.java

```
package com.mysite.controller;

import java.util.List;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RestController;
import com.mysite.domain.MemberVO;
import com.mysite.service.MemberService;

@RestController
public class MemberController {
	
	private MemberService memberService;
	
	public MemberController() {
		memberService = new MemberService();
	}
	
	@GetMapping("/Member")
	public List<MemberVO> getMembers(){
		return memberService.getMembers();
	}
	
	@GetMapping("/Member/{id}")
	public MemberVO getMember(@PathVariable String id) {
		return memberService.getMember(id);	
	}
	
	@PostMapping("/Member")
	public MemberVO addMember(MemberVO m) {
		return memberService.addMember(m);
	}
	
	@PutMapping("/Member")
	public MemberVO updateMember(MemberVO m) {
		return memberService.updateMembers(m);
	}
	
	@DeleteMapping("/Member/{id}")
	public MemberVO removeMember(@PathVariable String id) {
		return memberService.removeMember(id);
	}
	
}
```


