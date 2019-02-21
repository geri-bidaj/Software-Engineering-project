# Software-Engineering-project
This project is a management application for a restaurant.

package restaurant;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.ArrayList;
import java.util.logging.Level;
import java.util.logging.Logger;
import javax.swing.JFileChooser;

public class BarRestaurant {
    
    public static int next_id = 1 ;
    //Static members for applyimg discount to items
    public static Discount noDiscount;
    public static Discount discount;
    public static Discount christmasDiscount;
    public static Discount buyMorePayLess;
    //static members to store tables (serving,served) , items , current customers and users such as waiters and managers.
    public static ArrayList<Table> currently_served_tables;
    public static ArrayList<Table> served_and_paid_tables;
    public static ArrayList<Item> items;
    public static ArrayList<User> users;
    public static ArrayList<User> customers;
    
	//static members to hold current values of discount.
    public static double specialDis;
    public static double christmasDis;
    public static int buyDis;
    public static int payDis;
    
    public static Discount[] discounts;
    //to hold current user.
    public static User loggedInUser;
    //static block to initialize data members.
    static {
        
        currently_served_tables=new ArrayList<>();
        served_and_paid_tables=new ArrayList<>();
        customers=new ArrayList<>();
        items=new ArrayList<>();
        users=new ArrayList<>();
    
        specialDis=0.15;
        christmasDis=0.20;
        buyDis=3;
        payDis=2;
    
        noDiscount=new Discount(0);
        discount=new SpecialDiscount(specialDis);
        christmasDiscount=new ChristmasDiscount(specialDis,christmasDis);
        buyMorePayLess=new BuyMorePayLess(buyDis,payDis);
        
        discounts=new Discount[4];
        discounts[0]=noDiscount;
        discounts[1]=discount;
        discounts[2]=christmasDiscount;
        discounts[3]=buyMorePayLess;
        
        customers.add(new CompanyCustomer("James Franco"));
        customers.add(new CompanyCustomer("Clint Eastwood"));
        customers.add(new CustomerEmployee("Mathew Perry"));
        customers.add(new CustomerEmployee("Rob Stark"));
        customers.add(new LoyalCustomer("David Schwimmer"));
        customers.add(new LoyalCustomer("Ragnar Lothbrok"));
        customers.add(new Customer("Sheldon Cooper"));
        customers.add(new Customer("Thomas Shelby"));
        
        items.add(new Item("Espresso",1,3.0,noDiscount));
        items.add(new Item("Latte",2,4.5,discount));
        items.add(new Item("Apple Pie",3,15.0,christmasDiscount));
        items.add(new Item("Peach Cobbler",4,15.0,buyMorePayLess));
        items.add(new Item("Nachos",5,3.0,noDiscount));
        items.add(new Item("Egg Roll",6,3.0,buyMorePayLess));
        items.add(new Item("Chocolate Chip Cookies",7,2.5,christmasDiscount));
        items.add(new Item("Green Tea",8,2.0,noDiscount));
        items.add(new Item("Bagel",9,5.0,buyMorePayLess));
        items.add(new Item("Chocolate Doughnut",10,4.5,discount));
        
        users.add(new Waiter("John", "Watson", 12, 7, "123", "Watson", "12345", "Harward", "(123)456-7890"));
        users.add(new Waiter("Anney", "Hatheway", 12, 7, "123", "Hatheway", "12345", "Harward", "(123)456-7890"));
        users.add(new Waiter("Julia", "Roberts", 12, 7, "123", "Roberts", "12345", "Harward", "(123)456-7890"));
        users.add(new Waiter("Barack", "Obama", 10, 8, "123", "Obama", "12345", "Harward", "(123)456-7890"));
        users.add(new Waiter("Steve", "Jobs", 11, 9, "123", "Jobs", "12345", "Harward", "(123)456-7890"));
        
        users.add(new Manager("Harry", "Potter", "456", "Potter", "67890", "Hogwards", "(111)123-1230"));
        users.add(new Manager("Hermoine", "Grenjer", "466", "Hermoine", "67890", "Hogwards", "(111)123-1230"));
        users.add(new Manager("Ron", "Wesley", "476", "Wesley", "67890", "Hogwards", "(111)123-1230"));
        users.add(new Manager("Elbus", "Dumbledore", "486", "Dumbledore", "67890", "Hogwards", "(111)123-1230"));
        users.add(new Manager("Sirius", "Black", "496", "Black", "67890", "Hogwards", "(111)123-1230"));
        
        
    }
	//method to save current state of program to file.
    public static void saveData(){
        String wd = System.getProperty("user.dir");
        //this.setAlwaysOnTop(false);
        JFileChooser fc = new JFileChooser(wd);

        fc.setDialogType((int) JFileChooser.SAVE_DIALOG);

        int rc = fc.showDialog(null, "Save Data");
        //this.setAlwaysOnTop(true);

        if (rc == JFileChooser.APPROVE_OPTION) {
            try {
                File file = fc.getSelectedFile();
                ObjectOutputStream output = null;
                output = new ObjectOutputStream(new FileOutputStream(file));
                output.writeObject(BarRestaurant.next_id);
                output.writeObject(specialDis);
                output.writeObject(christmasDis);
                output.writeObject(buyDis);
                output.writeObject(payDis);
                output.writeObject(noDiscount);
                output.writeObject(discount);
                output.writeObject(christmasDiscount);
                output.writeObject(buyMorePayLess);
                output.writeObject(discounts);
                output.writeObject(currently_served_tables);
                output.writeObject(served_and_paid_tables);
                output.writeObject(customers);
                output.writeObject(items);
                output.writeObject(users);
                output.writeObject(MainWindow.table);
                output.close();
            } catch (IOException ex) {
                Logger.getLogger(BarRestaurant.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
    }
	//method to load/restore saved state of the objects 
    public static void loadData(){
        String wd = System.getProperty("user.dir");
        //this.setAlwaysOnTop(false);
        JFileChooser fc = new JFileChooser(wd);
        fc.setDialogType((int) JFileChooser.OPEN_DIALOG);
        int rc = fc.showDialog(null, "Load Data");
        //this.setAlwaysOnTop(true);
        if (rc == JFileChooser.APPROVE_OPTION) {
            try {
                File file = fc.getSelectedFile();
                String filename = file.getAbsolutePath();
                ObjectInputStream input = null;
                input = new ObjectInputStream(new FileInputStream(file));
                BarRestaurant.next_id=(int) input.readObject();
                specialDis=(double) input.readObject();
                christmasDis=(double) input.readObject();
                buyDis=(int) input.readObject();
                payDis=(int) input.readObject();
                noDiscount=(Discount) input.readObject();
                discount=(Discount) input.readObject();
                christmasDiscount=(Discount) input.readObject();
                buyMorePayLess=(Discount) input.readObject();
                discounts=(Discount[]) input.readObject();
                currently_served_tables=(ArrayList<Table>) input.readObject();
                served_and_paid_tables=(ArrayList<Table>) input.readObject();
                customers=(ArrayList<User>) input.readObject();
                items=(ArrayList<Item>) input.readObject();
                users=(ArrayList<User>) input.readObject();
                MainWindow.table=(Table) input.readObject();
                MainWindow.initTable();
                input.close();
            } catch (FileNotFoundException ex) {
                Logger.getLogger(BarRestaurant.class.getName()).log(Level.SEVERE, null, ex);
            } catch (IOException ex) {
                Logger.getLogger(BarRestaurant.class.getName()).log(Level.SEVERE, null, ex);
            } catch (ClassNotFoundException ex) {
                Logger.getLogger(BarRestaurant.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
    }
	//method to compute number of tables that have been served by the given chasier name 
    public static int countTables(String cashierName){
        int count=0;
        for (Table table : served_and_paid_tables) {
            if(table.getCashierName().equals(cashierName))
                count++;
        }
        return count;
    }
    //method to compute total amount of tables that have been served by the given chasier name
    public static double amountOfTables(String cashierName){
        double amount=0;
        for (Table table : served_and_paid_tables) {
            if(table.getCashierName().equals(cashierName))
                amount+=table.getTotalAmount();
        }
        return amount;
    }
    //method to login a user.
    public static boolean login(String username, String password) {

        for (User user : users) {
            if (user.getUsername().equals(username) && user.getPassword().equals(password)) {
                loggedInUser = user;
                return true;
            }
        }
        return false;
    }
    //method to retrieve table for given id.
    public static Table getTableById(int id){
        for (Table table : currently_served_tables) {
            if(table.getId()==id)
                return table;
        }
        for (Table table : served_and_paid_tables) {
            if(table.getId()==id)
                return table;
        }
        return null;
    }
    //method to retrieve item by given name
    public static Item getItemByName(String itemName){
        
        for (Item item : items) {
            if(item.getName().equals(itemName)){
                return item;
            }
        }
        return null;
    }
    //method to get names of all the current customers
    public ArrayList<String> getCustomerNames(){
        ArrayList<String> names=new ArrayList<>();
        customers.forEach(customer->{
            names.add(customer.getUsername());
        });
        return names;
    }
    //method to change the state of table from being served to served.
    public static void moveToServed(Table table){
        currently_served_tables.remove(table);
        table.calculatePayment();
        served_and_paid_tables.add(table);
    }
	//method to apply discount, compute new amount and change the state of table from being served to served.
    public static void moveToServedWithDiscount(Table table){
        currently_served_tables.remove(table);
        table.applyDiscount();
        table.calculatePayment();
        served_and_paid_tables.add(table);
    }
    //method to count all the items that have been served by the given waiter
    public static int countItems(String cashierName){
        int count=0;
        for (Table table : served_and_paid_tables) {
            if(table.getCashierName().equals(cashierName))
                for(TableItem item:table.items){
                    count+=item.quantity;
                }
        }
        return count;
    }
	//method to compute total amount of transactions of given date.
    public static double calculateAmountOfItems(String date){
        double amount=0;
        for (Table table : served_and_paid_tables) {
            if(table.getDate().contains(date))
                amount+=table.getTotalAmount();
        }
        return amount;
    }
    
}


package restaurant;

import java.io.Serializable;


public class BuyMorePayLess extends Discount implements Serializable{

    private int itemsToBuy; // buy this amount of items to get discount
    private int itemsDiscounted;//pay for this amount of items 
    
    @Override
    public String toString() {
        return "Buy More Pay Less";
    }
    //overloaded constructors
    public BuyMorePayLess(int itemsToBuy,int itemsDiscounted) {
        super(0);
        this.itemsDiscounted=itemsDiscounted;
        this.itemsToBuy=itemsToBuy;
    }
	//getters and setters.
    public int getItemsToBuy() {
        return itemsToBuy;
    }

    public void setItemsToBuy(int itemsToBuy) {
        this.itemsToBuy = itemsToBuy;
    }

    public int getItemsDiscounted() {
        return itemsDiscounted;
    }

    public void setItemsDiscounted(int itemsDiscounted) {
        this.itemsDiscounted = itemsDiscounted;
    }
	//overriden method to calculate total price with discount given the price and quantity of item.
    @Override
    public double calculate(double price, int quantity) {
        int toPay=quantity/itemsToBuy;
        quantity-=toPay*itemsToBuy;
        toPay*=itemsDiscounted;
        
        return (price*quantity)+(toPay*price);
    }
}



package restaurant;

import java.io.Serializable;

public class ChristmasDiscount extends SpecialDiscount implements Serializable{

    //overloaded constructor
    public ChristmasDiscount(double discount,double christmasDiscount) {
        super(discount+christmasDiscount);
    }
	//overriden method to calculate price with discount given the price and quantity of item
    @Override
    public double calculate(double price, int quantity) {
        return super.calculate(price, quantity); //To change body of generated methods, choose Tools | Templates.
    }
    //overriden toString method to return name of the discount
    @Override
    public String toString() {
        return "Christmas Discount";
    }
}


package restaurant;

import java.io.Serializable;
import java.util.ArrayList;

public class CompanyCustomer extends Customer implements Serializable{

    int tablesVisited;
	//constructors.
    public CompanyCustomer() {
        tablesVisited=0;
    }

    public CompanyCustomer(String username) {
        super(username);
        tablesVisited=0;
    }

    //factory method to return table associated with the customer
    @Override
    public Table getTable() {
        Table table=new TableCompany();
        addTable(table);
        return table;
    }
    //calculate bonus a customer.
    @Override
    double calculateBonus() {
        int totalTimesServed=0;
        int i=tablesVisited;
        ArrayList<Table> tables = getTables();
        for (; i < tables.size(); i++) {
            Table table = tables.get(i);
            totalTimesServed+=((TableCompany)table).getNumberOfTimesServed();
        }
        tablesVisited=i;
        
        return -((totalTimesServed/300)*100);
    }
    
}


package restaurant;

import java.io.Serializable;
import java.util.ArrayList;


public class Customer extends User implements Serializable{
    private ArrayList<Table> tables;
    //constructors.
    public Customer() {
        tables=new ArrayList<>();
    }
    public Customer(String username){
		// data initialization
        super(username,"12345","Street#1, City ABC, Brakebills","(123)456-7890");
        tables=new ArrayList<>();
    }
	//get all tables of a customer
    public ArrayList<Table> getTables(){
        return tables;
    }

    //factory method to return table associated with the customer
    public Table getTable(){
        Table currentTable=new Table();
        addTable(currentTable);
        return currentTable;
    }
	//add a table to tables of a customer.
    public void addTable(Table table){
        tables.add(table);
    }
	//calculate bonus a customer
    @Override
    double calculateBonus(){
        return 0;
    }

    @Override
    public String toString() {
        return this.getUsername();
    }
}



package restaurant;

import java.io.Serializable;
import java.util.ArrayList;

public class CustomerEmployee extends Customer implements Serializable{
    private int tablesVisited;

	//constructors
    public CustomerEmployee() {
        tablesVisited=0;
    }
    
    public CustomerEmployee(String username) {
        super(username);
    }

    //factory method to return table associated with the customer
    @Override
    public Table getTable() {
        Table table=new TableEmployee();
        addTable(table);
        return table;
    }
	//to calculate bonus of a customer.
    @Override
    double calculateBonus() {
        int totalAmount=0;
        
        int i=tablesVisited;
        ArrayList<Table> tables = getTables();
        for (; i < tables.size(); i++) {
            Table table = tables.get(i);
            totalAmount+=((TableEmployee)table).getAmount();
        }
        tablesVisited=i;
        
        return (totalAmount/500)*50;
    }

    
}


package restaurant;

import java.io.Serializable;

public class Discount implements Serializable{
    private double discount;
    
	//overriden toString method to return name of the discount.
    @Override
    public String toString() {
        return "No Discount";
    }
    //constructor
    public Discount() {
        discount=0.0;
    }
	//constructor for base classes to invoke and set discount.
    public Discount(double discount) {
        this.discount = discount;
    }

    //getters and setters.
    public double getDiscount() {
        return discount;
    }

    public void setDiscount(double discount) {
        this.discount = discount;
    }
	//overriden method to calculate price with discount given the price and quantity of item
    public double calculate(double price,int quantity){
        return (price*quantity)-(discount)*(price*quantity);
    }
}



package restaurant;

import java.io.Serializable;


public class Item implements Cloneable,Serializable{
    private String name;//name of the item
    private final int id;
    private double price;//price of an item
    
    private Discount discount;//type of discount on item 

    static int next_id=1; //This is used to set the ID for each item.

	//getters, setters and constructors.
    public Discount getDiscount() {
        return discount;
    }

    public void setDiscount(Discount discount) {
        this.discount = discount;
    }
    
    public Item(){
        id=next_id++;
    }

    public Item(int id) {
        this.id = id;
    }

    public Item(String name, int id, double price, Discount discount) {
        this.name = name;
        this.id = id;
        this.price = price;
        this.discount = discount;
    }
    
    public Item(Item item){
        this.id=item.getId();
        this.name=item.getName();
        this.price=item.getPrice();
    }
    String printInfo(){
        return "ID: "+getId()+" , Name: "+getName()+" , Price: "+getPrice();
    }

    /**
     * @return the name
     */
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    /**
     * @return the id
     */
    public int getId() {
        return id;
    }

    /**
     * @return the price
     */
    public double getPrice(int quantity) {
        return discount.calculate(price, quantity);
    }

    public double getPrice() {
        return price;
    }
    
    
    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        super.clone();
        return new Item(this);
    }

    @Override
    public String toString() {
        return this.getName();
    }
}



package restaurant;

import java.io.Serializable;
import java.util.ArrayList;

public class LoyalCustomer extends Customer implements Serializable{
    private int tablesVisited;//helper member to know values for which tables have already been computed.

    
    public LoyalCustomer() {
        tablesVisited=0;
    }
    //factory method to return table associated with the customer
    @Override
    public Table getTable() {
        Table table=new TableFidelity();
        addTable(table);
        return table;
    }
	//to calculate bonus of a customer.
    @Override
    double calculateBonus() {
        int totalPoints=0;
        
        int i=tablesVisited;
        ArrayList<Table> tables = getTables();
        for (; i < tables.size(); i++) {
            Table table = tables.get(i);
            totalPoints+=((TableFidelity)table).getPoints();
        }
        tablesVisited=i;
        
        return -((totalPoints/100)*50);
    }

    public LoyalCustomer(String username) {
        super(username);
    }
    
}



package restaurant;

import java.io.Serializable;

public class Manager extends User implements Serializable{
    private String name;
    private String surname;
    private String internal_phone_number;
    
    public Manager(){
        
    }
    
    public Manager(String name, String surname, String internal_phone_number, String username, String password, String address, String phone_number) {
        super(username, password, address, phone_number);
        this.name = name;
        this.surname = surname;
        this.internal_phone_number = internal_phone_number;
    }
    
    
    @Override
    String printInfo() {
        return super.printInfo()+" , Internal Phone Number: "+internal_phone_number; 
    }
	//no bonus is given to the manager.
    @Override
    double calculateBonus() {
        return 0;
    }
	//getters and setters.
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSurname() {
        return surname;
    }

    public void setSurname(String surname) {
        this.surname = surname;
    }

    public String getInternalPhoneNumber() {
        return internal_phone_number;
    }

    public void setInternalPhoneNumber(String internal_phone_number) {
        this.internal_phone_number = internal_phone_number;
    }
}


package restaurant;

public interface Payable {
	//method to calculate payment due.
    double calculatePayment();
}


package restaurant;

import java.io.Serializable;

public class SpecialDiscount extends Discount implements Serializable{

	//constructor
    public SpecialDiscount(double discount) {
        super(discount);
    }
    //overriden method to return name of the discount.
    @Override
    public String toString() {
        return "Special Discount";
    }

	//overriden method to calculate price with discount given the price and quantity of item
    @Override
    public double calculate(double price, int quantity) {
        return super.calculate(price, quantity);
    }
}



package restaurant;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.Iterator;

public class Table implements Payable,Serializable{
    private int id;  //autoincremental id
    private double net_amount;//net total amount of the table
    private double total_amount; //total amount including VAT
    private double VAT; //Value Added Tax
    private String date; //date or purchase
    private String time_of_purchase; 
    private String restaurant_address; 
    private String cashier_name;
    public ArrayList<TableItem> items;//all the items in the tables servinga
    private boolean isServed;//is the current table being served or has been served.
    private double discount;//discount set on the table
    
	//constructor
    public Table(){
        id=BarRestaurant.next_id++;
        items=new ArrayList<>();
        discount=0;
    }
	//getters and setters.
    public int getId() {
        return id;
    }

    public double getNetAmount() {
        return net_amount;
    }

    public double getTotalAmount() {
        return total_amount;
    }

    public double getVAT() {
        return VAT;
    }

    public void setVAT(double VAT) {
        this.VAT = VAT;
    }

    public String getDate() {
        return date;
    }

    public void setDate(String date) {
        this.date = date;
    }

    public String getTimeOfPurchase() {
        return time_of_purchase;
    }

    public void setTimeOfPurchase(String time_of_purchase) {
        this.time_of_purchase = time_of_purchase;
    }

    public String getRestaurantAddress() {
        return restaurant_address;
    }

    public void setRestaurantAddress(String restaurant_address) {
        this.restaurant_address = restaurant_address;
    }

    public String getCashierName() {
        return cashier_name;
    }

    public void setCashierName(String cashier_name) {
        this.cashier_name = cashier_name;
    }
	//get all items of the table.
    public ArrayList<Item> getAllItems() {
        ArrayList<Item> _items=new ArrayList<>();
        items.stream().forEach((item) -> {
            _items.add(item.getItem());
        });
        return _items;
    }
	//get names of all the items of the table.
    public ArrayList<String> getAllItemNames(){
        ArrayList<String> names=new ArrayList<>();
        items.stream().forEach((item) -> {
            names.add(item.getItem().getName());
        });
        return names;
    }
    //remove pre existing item and add a new one
    public void modifyItem(Item oldItem,Item newItem,int newQuantity){
        for (Iterator<TableItem> it = items.iterator(); it.hasNext();) {
            TableItem item = it.next();
            if(item.getItem().getName().equals(oldItem.getName())){
                it.remove();
                break;
            }
        }
        TableItem item=new TableItem(newItem, newQuantity);
        items.add(item);
    }
	//add item to table given its quantity
    public void addItem(Item item,int quantity){
        for (TableItem tableItem : items) {
            if(tableItem.getItem().getName().equals(item.getName())){
               tableItem.increaseQuantity(quantity);
               return;
            }
        }
        TableItem tblitm=new TableItem(item, quantity);
        this.items.add(tblitm);
    }
	//add item to table with quantity 1
    public void addItem(Item item){
        for (TableItem tableItem : items) {
            if(tableItem.getItem().getName().equals(item.getName())){
               tableItem.increaseQuantity(1);
               return;
            }
        }
        TableItem tblitm=new TableItem(item, 1);
        this.items.add(tblitm);
    }
	//remove an item from the table 
    public boolean removeItem(Item item){
        for (Iterator<TableItem> it = items.iterator(); it.hasNext();) {
            TableItem _item = it.next();
            if(_item.getItem().getName().equals(item.getName())){   
                it.remove();
                return true;
            }
        }
        return false;
    }
	//apply 10% discount on this table.
    public void applyDiscount(){
        discount=0.1;
    }
	//method of interface Payable implemented to calculate due payment.
    @Override
    public double calculatePayment() {
        double total=0;
        total = items.stream().map((item) -> item.getItem().getPrice(item.getQuantity())).reduce(total, (accumulator, _item) -> accumulator + _item);
        net_amount=total;
        total_amount=total+(VAT*net_amount);
        total_amount-=(discount*total_amount);
        return total_amount;
    }
	
    public boolean isServed() {
        return isServed;
    }

    public void setIsServed(boolean isServed) {
        this.isServed = isServed;
    }
}



package restaurant;

import java.io.Serializable;

public class TableCompany extends Table implements Serializable{
    //Number of times the employees of the company have been served. 
    //Every 300 different items there is a table free of charge with value not greater than 100 Euros.
    private int numberOfTimesServed; 
	//constructor.
    public TableCompany() {
        numberOfTimesServed=0;
    }
    //getters and setters.
    public void incrementServings(){
        numberOfTimesServed++;
    }
    
    public int getNumberOfTimesServed() {
        return numberOfTimesServed;
    }

    public void setNumberOfTimesServed(int numberOfTimesServed) {
        this.numberOfTimesServed = numberOfTimesServed;
    }
}



package restaurant;

import java.io.Serializable;


public class TableEmployee extends Table implements Serializable{
    //If employees in more tables reach the amount of 500 Euros, they get a bonus in their salary of 50 euros
    private int amount; //total amount spent by an employee in the restaurant

	//getters and setters.
    public int getAmount() {
        return amount;
    }

    public void setAmount(int amount) {
        this.amount = amount;
    }
    
}



package restaurant;

import java.io.Serializable;

public class TableFidelity extends Table implements Serializable{
    //points: The accumulation of points for the customer. 10 Euro count for 1 point. Every 100 points, the customer gets a bonus of 50 Euro.
    private int points;//total points earned by a Loyal Customer

	//getters and setters
    public int getPoints() {
        return points;
    }

    public void setPoints(int points) {
        this.points = points;
    }
    
}



package restaurant;

import java.io.Serializable;

public class TableItem  implements Serializable{
    Item item;//item on the table 
    int quantity;//quantity of the item

	//constructor.
    public TableItem(Item item, int quantity) {
        this.item = item;
        this.quantity = quantity;
    }

	//getters and setters.
    public Item getItem() {
        return item;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setItem(Item item)  {
        this.item=item;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }
    public void increaseQuantity(int quantity){
        this.quantity+=quantity;
    }
}



package restaurant;

import java.io.Serializable;
import java.util.ArrayList;
import javax.swing.table.AbstractTableModel;

public class TableModel extends AbstractTableModel  implements Serializable{
    
    Table table;
    String[] header={"Item Name","Quantity"};
    
    TableModel(Table table){
        this.table=table;
    }
    @Override
    public int getRowCount() {
        return table.items.size();
    }

    @Override
    public int getColumnCount() {
        return header.length;
    }

    @Override
    public Object getValueAt(int rowIndex, int columnIndex) {
        TableItem item=table.items.get(rowIndex);
        switch(columnIndex){
            case 0:
                return item.item.getName();
            case 1:
                return item.quantity;
        }
        return null;
    }
    
    @Override
    public String getColumnName(int col) {
        return header[col] ;
    }
}



package restaurant;

import java.io.Serializable;

public abstract class User implements Serializable{
    private static int next_id=1;
    private int id;//autoincremental id
    private String username;//username of the user.
    private String password;//password set by the system of the user.
    private String address; //address of the user
    private String phone_number;//phone number
    
    public User(){
        id=next_id++;
    }

    public User(String username, String password, String address, String phone_number) {
        this.id = next_id++;
        this.username = username;
        this.password = password;
        this.address = address;
        this.phone_number = phone_number;
    }
    
    //abstract method for childs to override and calculate bonus.
    abstract double calculateBonus();
	//print the info of the user
    String printInfo(){
        return "ID: "+id+" , Name: "+username+" , Address: "+address+" , Phone Number: "+phone_number;
    }
	//getters and setters.
    public int getId() {
        return id;
    }
    
    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getPhone_number() {
        return phone_number;
    }

    public void setPhone_number(String phone_number) {
        this.phone_number = phone_number;
    }
}



package restaurant;

import java.io.Serializable;

public class Waiter extends User implements Payable,Serializable{
    private String name;//name of the user.
    private String surname;//surname of the user
    
    private float wage_per_hour; 
    private int hours_of_work;
    private int total_hours;//total hours a waiter worked
    private String internal_phone_number;
    
	//constructor
    public Waiter(String name, String surname, float wage_per_hour, int total_hours, String internal_phone_number, String username, String password, String address, String phone_number) {
        super(username, password, address, phone_number);
        this.name = name;
        this.surname = surname;
        this.wage_per_hour = wage_per_hour;
        this.total_hours = total_hours;
        this.internal_phone_number = internal_phone_number;
    }

    //method to calculate bonus if any of the waiter.
    @Override
    double calculateBonus() {
        return (total_hours-hours_of_work)*5;
    }
    
    @Override
    String printInfo() {
        return super.printInfo()+" , Internal Phone Number: "+internal_phone_number+" , Wage/Hour: "+wage_per_hour+" , Hours of Work: "+hours_of_work;
    }
	//getters and setters.
    public int getHoursOfWork() {
        return hours_of_work;
    }

    public void setHoursOfWork(int hours_of_work) {
        this.hours_of_work = hours_of_work;
    }

    public float getWagePerHour() {
        return wage_per_hour;
    }

    public void setWagePerHour(float wage_per_hour) {
        this.wage_per_hour = wage_per_hour;
    }

    public String getInternalPhoneNumber() {
        return internal_phone_number;
    }

    public void setInternalPhoneNumber(String internal_phone_number) {
        this.internal_phone_number = internal_phone_number;
    }

    public int getTotalHours() {
        return total_hours;
    }

    public void setTotalHours(int total_hours) {
        this.total_hours = total_hours;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSurname() {
        return surname;
    }

    public void setSurname(String surname) {
        this.surname = surname;
    }
	//method to comoute salary of a waiter.
    @Override
    public double calculatePayment() {
        return (wage_per_hour*hours_of_work)+calculateBonus();
    }
    
    
}
