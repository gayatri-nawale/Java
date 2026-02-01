# Hibernate -postgresql

1. import dependancy in pom.xml
2. make hibernate.cfg.xml
3. Create class (table in database , mark as entity, give PK, define fields)
4. write configuration in main file

```cpp
<hibernate-configuration xmlns="http://www.hibernate.org/xsd/orm/cfg">
    <session-factory>
        <property name="hibernate.connection.url">jdbc:postgresql://localhost:5432/Person</property>
        <property name="hibernate.connection.username">postgres</property>
        <property name="hibernate.connection.password">Pass123</property>
        <property name="hibernate.connection.driver_class">org.postgresql.Driver</property>

        <property name="hbm2ddl.auto">update</property>
        <property name="hibernate.dialect">org.hibernate.dialect.PostgreSQLDialect</property>

        <mapping class="com.gayatri.Personal_Info"/>
    </session-factory>
</hibernate-configuration>
```

```cpp
//Basic Crud operation
package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;
public class App 
{
    public static void main( String[] args )
    {
        Personal_Info p1=new Personal_Info();
        p1.setPid(112);
        p1.setAge(12);
        p1.setName("Neha kumar");

        Transaction t=null;
       try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
           Session session=sf.openSession();
           t=session.beginTransaction();
           // Saving data (Create)
//           session.persist(p1);
//           System.out.println(p1);

           // Display data (Read)
           Personal_Info p2=  session.find(Personal_Info.class,112);
           System.out.println(p2.getName()+" - "+p2.getAge());

           // Update
//           p2.setAge(22);
//           session.merge(p2);
//           System.out.println("database updated ");

           // Delete
//           Personal_Info p3=session.find(Personal_Info.class,123);
//           session.remove(p3);
//           System.out.println("Person id "+ p3.getPid()+" deleted successfully");
           
           t.commit();
           session.close();
       }catch(Exception e){
           if(t!=null){
                t.rollback();
           }
       }
        System.out.println("Connection closed...");
    }
}

```

- @embeddable
    - mark another table (to join embeddable)
    - main table make field of embedable class
    
    ```cpp
    package com.gayatri;
    
    import org.hibernate.Session;
    import org.hibernate.SessionFactory;
    import org.hibernate.Transaction;
    import org.hibernate.cfg.Configuration;
    
    /**
     * Hello world!
     *
     */
    public class App 
    {
        public static void main( String[] args )
        {
            Address a1=new Address();
            a1.setCity("Sangamner");
            a1.setState("Maharashtra");
            a1.setPincode(1234);
    
            Personal_Info p1=new Personal_Info();
            p1.setPid(321);
            p1.setAge(32);
            p1.setName("Ursha");
            p1.setAddress(a1);
    
            Transaction t=null;
           try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
               Session session=sf.openSession();
               t=session.beginTransaction();
               session.persist(p1);
               t.commit();
               session.close();
           }catch(Exception e){
               if(t!=null){
                    t.rollback();
               }
           }
            System.out.println("Connection closed...");
        }
    }
    
    ```
    

- one to one

```cpp
package com.gayatri;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args )
    {
        Address a1=new Address();
        a1.setCity("Sangamner");
        a1.setState("Maharashtra");
        a1.setPincode(1234);

        Contact c1=new Contact();
        c1.setMno(14384238);

        Personal_Info p1=new Personal_Info();
        p1.setPid(221);
        p1.setAge(45);
        p1.setName("Yogita gunjal");
        p1.setAddress(a1);
        p1.setContact(c1);

        Transaction t=null;
       try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
           Session session=sf.openSession();
           t=session.beginTransaction();
           session.persist(p1);
          session.persist(c1);
           t.commit();
           session.close();
       }catch(Exception e){
           if(t!=null){
                t.rollback();
           }
       }
        System.out.println("Connection closed...");
    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.OneToOne;

@Entity
public class Personal_Info {
    @Id
    private int pid;
    private String name;
    private int age;
    private Address address;
    @OneToOne
    private Contact contact;

    public Contact getContact() {
        return contact;
    }

    public void setContact(Contact contact) {
        this.contact = contact;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Personal_Info{" +
                "pid=" + pid +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public int getPid() {
        return pid;
    }

    public void setPid(int pid) {
        this.pid = pid;
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

```

```cpp
package com.gayatri;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Contact {
    @Id
    private long mno;

    public long getMno() {
        return mno;
    }

    public void setMno(long mno) {
        this.mno = mno;
    }
}
//mapp this in hibernate configuration
```

one to many 

many to one

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
        Address a1=new Address();
        a1.setCity("Pune");
        a1.setState("Maharashtra");
        a1.setPincode(4533);

        Contact c1=new Contact();
        c1.setMno(343232);

        Contact c2=new Contact();
        c2.setMno(3434343);

        Contact c3=new Contact();
        c3.setMno(867675);

        Personal_Info p1=new Personal_Info();
        p1.setPid(445);
        p1.setAge(32);
        p1.setName("John");
        p1.setAddress(a1);
     p1.setContact(Arrays.asList(c1,c2));

     c1.setPerson(p1);
     c2.setPerson(p1);

        Transaction t=null;
       try(SessionFactory sf=new Configuration().configure("hibernate.cfg.xml").buildSessionFactory()){
           Session session=sf.openSession();
           t=session.beginTransaction();
           session.persist(p1);

           t.commit();
           session.close();
       }catch(Exception e){
           if(t!=null){
                t.rollback();
           }
       }
        System.out.println("Connection closed...");
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
public class Contact {
    @Id

    private long mno;
    @ManyToOne
    @JoinColumn(name = "pid")
    private Personal_Info person;

    public Personal_Info getPerson() {
        return person;
    }

    public void setPerson(Personal_Info person) {
        this.person = person;
    }

    public long getMno() {
        return mno;
    }

    public void setMno(long mno) {
        this.mno = mno;
    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.Embeddable;

@Embeddable
public class Address {
    private long pincode;
    private String city;
    private String State;

    @Override
    public String toString() {
        return "Address{" +
                "pincode=" + pincode +
                ", city='" + city + '\'' +
                ", State='" + State + '\'' +
                '}';
    }

    public long getPincode() {
        return pincode;
    }

    public void setPincode(long pincode) {
        this.pincode = pincode;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getState() {
        return State;
    }

    public void setState(String state) {
        State = state;
    }
}

```

```cpp
package com.gayatri;

import jakarta.persistence.*;

import java.util.List;

@Entity
public class Personal_Info {
    @Id
    private int pid;
    private String name;
    private int age;
    @Embedded
    private Address address;
    @OneToMany(mappedBy = "person", cascade = CascadeType.ALL)
    private List<Contact> contact;

    public List<Contact> getContact() {
        return contact;
    }

    public void setContact(List<Contact> contact) {
        this.contact = contact;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Personal_Info{" +
                "pid=" + pid +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public int getPid() {
        return pid;
    }

    public void setPid(int pid) {
        this.pid = pid;
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

```