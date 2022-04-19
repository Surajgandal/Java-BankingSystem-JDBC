package database;

import java.sql.Connection;
import java.lang.Throwable;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.time.LocalDate;
import java.util.Scanner;

public class BankingApplication {
	static Connection conn;
	public static void connectDb(){
		try {
			//1. Register the Driver Class
		Class.forName("com.mysql.cj.jdbc.Driver");
		
		//2. Create the connection
		String url="jdbc:mysql://localhost:3306/java_BankingSystem";
		String username="root";
		String password="";
		
		 conn= DriverManager.getConnection(url,username,password);
		}catch(Exception e) {
			e.printStackTrace();
		}
	}
	public static void disconnectDb(){
				try {
					conn.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
	}
	public static class MinimumBalanceException extends Exception{
		public MinimumBalanceException() {
			System.out.println("=====================================================");
			System.out.println("============================================");
			System.out.println("Minimum amount should be 5000 or greater than 5000 ");
			System.out.println("============================================");
			System.out.println("======================================================");
		}
	}
	public static void main(String[] args) {
		Scanner s = new Scanner(System.in);
		try {
			while(true) {
			System.out.println("=======Welcome to SMG bank=========");
			System.out.println("1. Open a Bank account");
			System.out.println("2. Perform transactions for an account");
			System.out.println("3. Exit the application");
			System.out.println("===================================");
			
			System.out.println("Enter your option::");
			int op=s.nextInt();
			if(op>3) { 
				System.out.println("==========Invalid Option=========");
				break;}
			else {
			int accountno;
			String name;	
			String phoneno;
			String DOB;
			int amount;
			int pin;
			
			
			connectDb();
			PreparedStatement ps = null;
			ResultSet rs = null;
			      switch(op) {
			      case 1:
			    	  System.out.println("==========Open a Bank account========");
			    	  System.out.println("Enter your name");
			    	  name=s.next();
			    	  System.out.println("Enter your Phone Number");
			    	  phoneno =s.next();
			    	  System.out.println("Enter your Date of birth");
			    	  DOB =s.next();
			    	  LocalDate dateOfBirth=LocalDate.parse(DOB);
			    	  System.out.println("Enter your opening Bank amount is Not less than 10000");
			    	  amount =s.nextInt();
			    	 
			    	  System.out.println("Enter pin to generate");
			    	  pin =s.nextInt();
			    	  

					  ps=conn.prepareStatement("insert into acholder(name,phoneno,DOB,pin,amount) values(?,?,?,?,?)");
			    	  ps.setString(1, name);
			    	  ps.setString(2, phoneno);
			    	  ps.setString(3, DOB);
			    	  ps.setInt(4, pin);
			    	  ps.setInt(5, amount);
					
			    	  int val= ps.executeUpdate();
			    	  
			    	  if(val>0) {
			    		  System.out.println("++++++++++++++++++++++++++++");
			    		  System.out.println("Accounts created sucessfully");
			    		  System.out.println("+++++++++++++++++++++++++++++");
			    	  }else
			    		  System.out.println("Not inserted");
			    	  break;
			      case 2:
			    	  System.out.println("Perform transactions for an account");
			    	  
			    	  long deposit;
			    	  long withdraw;
			    	  long currentbalance;
			    	  
			    	  
			    	  while(true) {
			    		  System.out.println("1. Deposit");
			    		  System.out.println("2. Withdraw");
			    		  System.out.println("3. Change the owner name");
			    		  System.out.println("4. Display the number of transactions and closing balance");
			    		  System.out.println("5. Exit");
			    		  System.out.println("Enter a choice");
			    		  int choice=s.nextInt();
			    		  if (choice>5) {
			    			  System.out.println("Invalid choice");
			    			  break;
			    		  }else {
			    			  switch(choice){
			    			  case 1:
			    				  System.out.println("/////////Deposit/////////");
			    				  System.out.println("Enter amount to deposit");
			    				  deposit=s.nextLong();
			    				  System.out.println("Enter your Phone Number");
			    				  phoneno=s.next();
			    				  System.out.println("Enter your pin");
			    				  pin=s.nextInt();
			    				  
			    				  ps=conn.prepareStatement("Select pin from acholder where phoneno=?");
			    				  ps.setString(1, phoneno);
			    				  rs=ps.executeQuery();
			    				  rs.next();
			    				  int pin1=rs.getInt(1);
			    				  
			    				  ps=conn.prepareStatement("Select amount from acholder where phoneno=?");
			    				  ps.setString(1, phoneno);
			    				  rs=ps.executeQuery();
			    				  rs.next();
       		    				  int amount1=rs.getInt(1);
			    				  
			    				  if(pin==pin1) {
			    					  withdraw=0;
			    					  
			    					  currentbalance=amount1+deposit;
			    					  
			    					  
			    					  
			    					  ps=conn.prepareStatement("update acholder set amount=? where phoneno=?");
			    					  ps.setLong(1, currentbalance);
			    					  ps.setNString(2, phoneno);
			    					  ps.executeUpdate();
			    					  
			    					  ps=conn.prepareStatement("insert into balance(deposit,withdraw,currentbalance,pin) values(?,?,?,?)");
			    					  ps.setLong(1, deposit);
			    					  ps.setLong(2, withdraw);
			    					  ps.setLong(3, currentbalance);
			    					  ps.setLong(4, pin);
			    					  int val1=ps.executeUpdate();
			    					  
			    					  if(val1>0) {
			    						  System.out.println("============================================================");
			    						  System.out.println("=======================Deposited Amount=====================");
			    						  System.out.println("============================================================");
			    					  }else {
			    						  System.out.println("Not Deposited");}
			    					  
			    				  }else {
			    					  System.out.println("#########################");
			    					  System.out.println("####Enter a Wrong Pin####");
			    					  System.out.println("#########################");
			    				  }
			    				  
			    				  break;
			    			  case 2:
			    				  System.out.println("/////////Withdraw/////////");
			    				  System.out.println("Enter amount to withdraw");
			    				  withdraw=s.nextLong();
			    				  System.out.println("Enter your Phone Number");
			    				  phoneno=s.next();
			    				  System.out.println("Enter your pin");
			    				  pin=s.nextInt();
			    				  
			    				  ps=conn.prepareStatement("Select pin from acholder where phoneno=?");
			    				  ps.setString(1, phoneno);
			    				  rs=ps.executeQuery();
			    				  rs.next();
			    				  int pin11=rs.getInt(1);
			    				  
			    				  ps=conn.prepareStatement("Select amount from acholder where phoneno=?");
			    				  ps.setString(1, phoneno);
			    				  rs=ps.executeQuery();
			    				  rs.next();
       		    				  int amount11=rs.getInt(1);
			    				  
			    				  if(pin==pin11) {
			    					  deposit=0;
			    					  
			    					  currentbalance=amount11-withdraw;
			    					  try {
			    						  if(currentbalance<5000) {
			    							 throw new MinimumBalanceException(); 
			    						  }
			    							  
			    						  
			    					  
			    					  ps=conn.prepareStatement("update acholder set amount=? where phoneno=?");
			    					  ps.setLong(1, currentbalance);
			    					  ps.setNString(2, phoneno);
			    					  ps.executeUpdate();
			    					  
			    					  ps=conn.prepareStatement("insert into balance(deposit,withdraw,currentbalance,pin) values(?,?,?,?)");
			    					  ps.setLong(1, deposit);
			    					  ps.setLong(2, withdraw);
			    					  ps.setLong(3, currentbalance);
			    					  ps.setLong(4, pin);
			    					  int val1=ps.executeUpdate();
			    					  
			    					  if(val1>0) {
			    						  System.out.println("============================================================");
			    						  System.out.println("=======================Amount Withdraw=====================");
			    						  System.out.println("============================================================");
			    					  }else {
			    						  System.out.println("Fail to withdraw amount");}
			    					  }catch(MinimumBalanceException e) {
			    						  System.out.println(e.getMessage());
			    					  }
			    				  }else {
			    					  System.out.println("#########################");
			    					  System.out.println("####Enter a Wrong Pin####");
			    					  System.out.println("#########################");
			    				  }
			    				  break;
			    			  case 3:
			    				  System.out.println("/////////Change the owner name/////////");
			    				  
			    				  System.out.println("Enter a phoneno");
			    				  phoneno= s.next();
			    				  System.out.println("Change your name");
			    				  String name1= s.next();
			    				  
			    				  ps=conn.prepareStatement("update acholder set name=? where phoneno=?");
			    				  ps.setString(1, name1);
			    				  ps.setString(2, phoneno);
			    				  val=ps.executeUpdate();
			    				   if(val>0)
			    					   System.out.println("==================Updated=====================");
			    				   else
			    					   System.out.println("===============Not updated/ phoneno not found====================");
			    				  break;
			    			  case 4:
			    				  System.out.println("/////////Display the number of transactions and closing balance/////////");
			    				  
			    				  System.out.println("Enter your pin");
			    				  pin=s.nextInt();
			    				  
			    				  ps=conn.prepareStatement("select * from acholder where pin=?");
			    				  ps.setInt(1, pin);
			    				  rs=ps.executeQuery();
			    				  rs.next();
			    				  System.out.println("''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''");
		    					  System.out.println("AccountNumber\t Name\t\t PhoneNumber");
		    					  System.out.println(rs.getInt(1)+"\t\t"
		    							             +rs.getString(2)+"\t\t"
		    							             +rs.getString(3));
		    					  System.out.println("'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''");
			    				
			    				  ps=conn.prepareStatement("select * from balance where pin=?");
			    				  ps.setInt(1, pin);
			    				  rs=ps.executeQuery();
			    				  
			    				  int count=0;
			    				  while(rs.next()) {
			    					  System.out.println("==+=======+========+========+=======+========+========+");
			    					  System.out.println("DepositeBalance\t WithdrawBalance\t CurrentBalance");
			    					  System.out.println(rs.getLong(1)+"\t\t\t"
			    							             +rs.getLong(2)+"\t\t\t"
			    							             +rs.getLong(3));
			    					  System.out.println("==+=======+========+========+=======+========+========+");
			    					  count++;
			    				  }System.out.println("Total number of transections::"+count);
			    				  break;
			    			  case 5:
			    				  System.out.println("/////////Exit/////////");
			    				  break;
			    			  }
			    		  }
			    	  }
			    	  
 
			    	  break;
			      case 3:
			    	  System.out.println("===========================");
			    	  System.out.println("Exit your application");
			    	  System.out.println("==========Thank you=========");
			    	  break;
			      default:
			    	  System.out.println("Invalid Choice");
			    	  break;
			     
			      }
			      disconnectDb();
              }	
			
        }
		}catch(Exception e) {
			e.printStackTrace();
		}
		
	}

}
