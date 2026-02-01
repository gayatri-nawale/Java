# hibernate -mysql

1. add dependancy in pom.xml
2. make hibernate.cfg.xml file in resource package

```cpp
<hibernate-configuration xmlns="http://www.hibernate.org/xsd/orm/cfg">
    <session-factory>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/Company</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">Pass@1909</property>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>

        <property name="hbm2ddl.auto">update</property>
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>

        <mapping class="com.gayatri.Company"/>
    </session-factory>
</hibernate-configuration>
```

1. make class (table - mark as entity)
2. 

```cpp
package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class App 
{
    public static void main( String[] args )
    {
        Company c1=new Company();
        c1.setEmpid(5);
        c1.setEname("Pranav");
        c1.setCity("Mumbai");
        c1.setDepartment("Sales");
        Transaction t=null;
        try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){   // try with resource - no need for manual closing
            Session session=sf.openSession();
          t= session.beginTransaction();

            // Saving data
//            session.persist(c1);

            // Reading data
            Company c2= session.find(Company.class,4);
            System.out.println(c2);

            // Update data
            c1.setEname("reha");
            session.merge(c1);

            // remove data
//            Company c3= session.find(Company.class,3);
//            session.remove(c3);

            t.commit();
            session.close();
            System.out.println("Connection closed");

        }catch(Exception e){
            if(t!=null){
                t.rollback();
            }
            e.printStackTrace();
        }
    }
}

```

- use of @Embedable

```cpp
package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class App 
{
    public static void main( String[] args )
    {
        Salary s1=new Salary();
        s1.setSid(01);
        s1.setSalary(110000);

        Company c1=new Company();
        c1.setEmpid(101);
        c1.setEname("Pooja");
        c1.setCity("Banglore");
        c1.setDepartment("IT");
        c1.setS(s1);

   try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
       Session s=sf.openSession();
       Transaction t=s.beginTransaction();
       s.persist(c1);

       t.commit();
       s.close();

   }catch(Exception e){

   }

    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.*;

@Entity
@Table(name="Company")
public class Company {
    @Id
    private int empid;

    @Column(name="Employee name",nullable = false)
    private String ename;
                                                        // Transient - if dont want to store field data in table
    private String Department;
    private String City;
    private Salary s;

    public Salary getS() {
        return s;
    }

    public void setS(Salary s) {
        this.s = s;
    }

    public int getEmpid() {
        return empid;
    }

    public void setEmpid(int empid) {
        this.empid = empid;
    }

    public String getEname() {
        return ename;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public String getDepartment() {
        return Department;
    }

    public void setDepartment(String department) {
        Department = department;
    }

    public String getCity() {
        return City;
    }

    public void setCity(String city) {
        City = city;
    }

    @Override
    public String toString() {
        return "Company{" +
                "empid=" + empid +
                ", ename='" + ename + '\'' +
                ", Department='" + Department + '\'' +
                ", City='" + City + '\'' +
                '}';
    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.Embeddable;

@Embeddable
public class Salary {
    private int sid;
    private long salary;

    public int getSid() {
        return sid;
    }

    public void setSid(int sid) {
        this.sid = sid;
    }

    public long getSalary() {
        return salary;
    }

    public void setSalary(long salary) {
        this.salary = salary;
    }
}

```

Mapping relationships 

1. one to one 
    1. make both entities 
    2. update hibernate file mapping class
    3. in company give annotation to variable of manager - @Onetoone

```cpp

package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class App 
{
    public static void main( String[] args )
    {
      // showing one to one relationship
        Manager m1=new Manager();
        m1.setMid(2);
        m1.setMname("rahul singh");

        Company c1=new Company();
        c1.setEmpid(105);
        c1.setEname("mena");
        c1.setCity("Banglore");
        c1.setDepartment("IT");
        c1.setM(m1);

   try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
       Session s=sf.openSession();
       Transaction t=s.beginTransaction();
       s.persist(m1);
       s.persist(c1);

       t.commit();
       s.close();

   }catch(Exception e){

   }

    }
}

```

- many to one and one to many

```cpp
package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

import java.util.Arrays;

public class App 
{
    public static void main( String[] args )
    {
 // one to many and many to one
        Manager m1 = new Manager();
        m1.setMid(6);
        m1.setMname("skfdf singh");

        Manager m2 = new Manager();
        m2.setMid(5);
        m2.setMname("ram patel");

        Company c1 = new Company();
        c1.setEmpid(111);
        c1.setEname("yogita");
        c1.setCity("Pune");
        c1.setDepartment("Development");
        m1.setCompany(c1);
        m2.setCompany(c1);
        c1.setM(Arrays.asList(m1, m2));
        
   try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
       Session s = sf.openSession();
       Transaction t = s.beginTransaction();
       s.persist(c1);
       t.commit();
       s.close();

   }catch(Exception e){

   }

    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.*;

import java.util.List;

@Entity
@Table(name="Company")
public class Company {
    @Id
    private int empid;

    @Column(name = "employee_name", nullable = false)
    private String ename;

    private String Department;
    private String City;
    @OneToMany(mappedBy = "company", cascade = CascadeType.ALL)
    private List<Manager> m;

    public List<Manager> getM() {
        return m;
    }

    public void setM(List<Manager> m) {
        this.m = m;
    }

    public int getEmpid() {
        return empid;
    }

    public void setEmpid(int empid) {
        this.empid = empid;
    }

    public String getEname() {
        return ename;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public String getDepartment() {
        return Department;
    }

    public void setDepartment(String department) {
        Department = department;
    }

    public String getCity() {
        return City;
    }

    public void setCity(String city) {
        City = city;
    }

    @Override
    public String toString() {
        return "Company{" +
                "empid=" + empid +
                ", ename='" + ename + '\'' +
                ", Department='" + Department + '\'' +
                ", City='" + City + '\'' +
                ", m=" + m +
                '}';
    }

}

```

```cpp
package com.gayatri;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;

@Entity
public class Manager {
    // one company can have many manager, but a manager belong to only one company
    @Id
    private int mid;
    private String mname;
    @ManyToOne
    @JoinColumn(name = "company_empid") // FK column
    private Company company;

    public Company getCompany() {
        return company;
    }

    public void setCompany(Company company) {
        this.company = company;
    }

    public int getMid() {
        return mid;
    }

    public void setMid(int mid) {
        this.mid = mid;
    }

    public String getMname() {
        return mname;
    }

    public void setMname(String mname) {
        this.mname = mname;
    }
}

```