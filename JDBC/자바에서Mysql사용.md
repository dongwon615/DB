# 자바에서 Mysql사용하려면
## JDBC Library 설치하고, 아래와같이 연결하면됨 드라이버 불러오고, connection을 만드는데 해당라인 잘보기. 이후 createstatement or preparestatement이용해서 활용하면 됨.
``` java

public class JDBC3 {
	public static void main(String[] args) {
		Connection con = null;
		Statement st = null;
		ResultSet rs = null;
		try {
//			1. Driver Loading
			Class.forName("com.mysql.cj.jdbc.Driver");
//		2. Connection
			con=DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/scott?serverTimezone=UTC&useUniCode=yes&characterEncoding=UTF-8","DB아이디","DB비밀번호");
//		3. Statement Create
			st = con.createStatement();
//		4. SQL Excute
			rs = st.executeQuery("select * from emp");
			while(rs.next()) {
				System.out.println(rs.getString("ename") + " , " +rs.getInt("sal"));
			}
		} catch (ClassNotFoundException | SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
//			5. close
			try {
				if(rs!=null) rs.close();
				if(st!=null) st.close();
				if(con!=null) con.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
}
```
## 상품 DB 만들어서 사용하는것(상품 DB 만들어져있는거)
``` sql
use scott;

select @@SQL_SAFE_UPDATES;
set SQL_SAFE_UPDATES = 0;


-- 1. 상품정보 저장하는 테이블 구성
create table product(
	pid char(8) primary key,
    pname varchar(50),
    price int
);

-- 2. 상품데이터를 5개 이상 저장하는 sql
insert into product
values('12120101', '삼성TV', 2000000),
('12345678', '엘지TV', 1000000),
('24583284', '삼성TV신형', 8200000),
('33553355', '노트북1', 100000),
('95654522', '내노트북', 950000),
('82314622', '우리집TV', 30000),
('98721641', '노트', 10000);

select * from product where 1=1;

-- 3. 상품 15프로 세일
select pid, pname, price*0.85 as price
from product
where 1=1;

-- 4. TV관련 제품 가격 20프로 인하하여 저장
update product set price = price*0.8
where pname like '%TV%';
select * from product where 1=1;

-- 5. 저장된 상품 가격의 총 금액을 출력
select sum(price) as 총금액
from product
where 1=1;
```

여기 위에 나오는 테이블 이용하여, 자바로 삽입 삭제 수정 조회 하는 코드 구현하였음
``` java
public class Product {
	private String pid;
	private String pname;
	private int price;
	
	public Product() {
		super();
	}

	public Product(String pid, String pname, int price) {
		super();
		this.pid = pid;
		this.pname = pname;
		this.price = price;
	}

	public String getPid() {
		return pid;
	}

	public void setPid(String pid) {
		this.pid = pid;
	}

	public String getPname() {
		return pname;
	}

	public void setPname(String pname) {
		this.pname = pname;
	}

	public int getPrice() {
		return price;
	}

	public void setPrice(int price) {
		this.price = price;
	}

	@Override
	public String toString() {
		return "Product [pid=" + pid + ", pname=" + pname + ", price=" + price + "]";
	}
	
}
```
``` java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;

public class ProductDAO {
	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	private Connection getConnection() throws SQLException {

		Connection con = DriverManager.getConnection(
				"jdbc:mysql://127.0.0.1:3306/scott?serverTimezone=UTC&useUniCode=yes&characterEncoding=UTF-8", "ssafy",
				"ssafy");
		return con;
	}
	
	public int insertProduct(Product p) throws SQLException {
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		String sql = "insert into product (pid, pname, price) values (?, ?, ?)";
		pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, p.getPid());
		pstmt.setString(2, p.getPname());
		pstmt.setInt(3, p.getPrice());
		return pstmt.executeUpdate();
	}
	public ArrayList<Product> searchAllProduct() throws SQLException {
		ArrayList<Product> list = new ArrayList<>();
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		String sql = "select * from product";
		pstmt = conn.prepareStatement(sql);
		rs = pstmt.executeQuery();
		while(rs.next()) {
			list.add(new Product(rs.getString("pid"),rs.getString("pname"),rs.getInt("price")));
		}
		return list;
	}
	public int deleteProduct(String pid) throws SQLException {
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		String sql = "delete from product where pid = ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, pid);
		return pstmt.executeUpdate();
	}

	public int updatePrice(String pid, int price) throws SQLException {
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		String sql = "update product set price = ? where pid = ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setInt(1, price);
		pstmt.setString(2, pid);
		return pstmt.executeUpdate();
	}

	public int updateName(String pid, String pname) throws SQLException {
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		String sql = "update product set pname = ? where pid = ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, pname);
		pstmt.setString(2, pid);
		return pstmt.executeUpdate();
	}

	public Product searchById(String pid) throws SQLException {
		ArrayList<Product> list = new ArrayList<>();
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		String sql = "select * from product where pid = ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, pid);
		rs = pstmt.executeQuery();
		rs.next();
		return new Product(rs.getString("pid"),rs.getString("pname"),rs.getInt("price"));
		
	}
	
	public ArrayList<Product> searchByName(String pname) throws SQLException {
		ArrayList<Product> list = new ArrayList<>();
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		String sql = "select * from product where pname like ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setString(1, "%" + pname + "%");
		rs = pstmt.executeQuery();
		while(rs.next()) {
			list.add(new Product(rs.getString("pid"),rs.getString("pname"),rs.getInt("price")));
		}
		return list;
	}

	public ArrayList<Product> searchByPrice(int lowerlimit, int higherlimit) throws SQLException {
		ArrayList<Product> list = new ArrayList<>();
		Connection conn = getConnection();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		String sql = "select * from product where price between ? and ?";
		pstmt = conn.prepareStatement(sql);
		pstmt.setInt(1,lowerlimit);
		pstmt.setInt(2,higherlimit);
		rs = pstmt.executeQuery();
		while(rs.next()) {
			list.add(new Product(rs.getString("pid"),rs.getString("pname"),rs.getInt("price")));
		}
		return list;
	}
}
```
``` java
import java.sql.SQLException;
import java.util.ArrayList;

public class Test {
	public static void main(String[] args) {
		ProductDAO pdao = new ProductDAO();
		try {
			System.out.println(pdao.deleteProduct("20000001"));
			// 상품 테이블 확인
			ArrayList<Product> list = pdao.searchAllProduct();
			for(Product p: list) {
				System.out.println(p);
			}
			// 상품추가
			System.out.println(pdao.insertProduct(new Product("20000001","zTV",10000000)));
			list = pdao.searchAllProduct();
			for(Product p: list) {
				System.out.println(p);
			}
			// 상품삭제
//			System.out.println(pdao.deleteProduct("20000001"));
//			list = pdao.viewAllProduct();
//			for(Product p: list) {
//				System.out.println(p);
//			}
			// 상품수정 (가격, 이름)
			System.out.println(pdao.updatePrice("20000001", 500000));
			System.out.println(pdao.updateName("20000001", "제트TV"));
			list = pdao.searchAllProduct();
			
			for(Product p: list) {
				System.out.println(p);
			}
			// 상세조회
			Product pd = pdao.searchById("12345678");
			System.out.println("id검색");
			System.out.println(pd);
			System.out.println("이름에 TV들어간것");
			list = pdao.searchByName("TV");
			for(Product p: list) {
				System.out.println(p);
			}
			System.out.println("가격이 0 - 500000 사이인것");
			list = pdao.searchByPrice(0,500000);
			for(Product p: list) {
				System.out.println(p);
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}

```
