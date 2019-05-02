# CSCI490-Lab_06

Android comes standard with an SQLite relational database, however working with the common libraries surrounding this can be quite cumbersome with lots of boilerplate code to write. Android developers are currently working on an improved API and abstraction layer that makes sending data in and out of the SQLite database "easy".

## Good Overview Resouce

### Problem

This lab will have you create a database, an entity class to represent our data, a dao to transport the data to and from the database, related AsyncTask classes to perform the database operations, and simple layout/activities to interact with our data layer. Databases usually will have more than one table, but the database will only contain one table to simplify the learning process. The database will contain a Person table with only one field name. We will write names to the database and retrieve all the names. This will require one Dao with two SQL statements. One insert and one **query

### Purpose

This lab will create a practical application of the Android Room Database theory covered in class.

### Jargon

First, it helps to cover some of the database jargon that might be covered.

Database - A collection of related tables
Table - Related data held in a structured format
Entity - An object in our system that we want to store in a Table
Dao - Data Access Object. The object that transports the data to/from database

### Steps

Add dependencies:

Add the following dependencies in the app:gradle file:
implementation "android.arch.persistence.room:runtime:1.0.0"
annotationProcessor "android.arch.persistence.room:compiler:1.0.0"
Create package structure:

Create packages to organize your classes. There are several methods of organization. Some developers prefer to organize by feature while others group by the architecture. The choice is yours, but by architecture is a simple way.

entities, data, asyncs (one possibility)
Create the classes

In the appropriate packages create the following:

Create the Entity class

Create Person.java and mark it with the @Entity annotation. Add the following attributes and make appropriate getters/setters
 @PrimaryKey(autoGenerate = true)
 private int id;

 private String name;
Create the Dao

Create an interface named PersonDao.java
Add the following sql/java code:
@Insert
void insertPerson(Person person);

@Query("SELECT * FROM Person")
List<Person> getAllPersons();
Create the Database class

Create an abstract class called LabDatabase.java
Edit the code to the following:
@Database(entities = {Person.class}, version = 1)
public abstract class LabDatabase extends RoomDatabase {

    public abstract PersonDao personDao();
}
Create the layout files:

Create an activity_persons.xml and place a ListView as the only View element (Refer to Lab 3 if you must)
<ListView
    ...
    android:dividerHeight="5.0sp"
    android:divider="@android:color/black"
    android:padding="45sp">
</ListView>
In activity_main.xml add an EditText and two buttons. One button to submit the name and one to list all names.
In MainActivity.java create OnClickListeners() to each button. Create Toast messages to ensure these work. Have the button that submits the name echo the name from the EditText in the Toast message.
Create the other Java files in appropriate packages

Create a PersonsActivity.java file and the following code inside the onCreate(). (This should seem familiar from Lab 3)
setContentView(R.layout.activity_persons);

listView = findViewById(R.id.personName);

ArrayList<String> i = (ArrayList) this.getIntent().getExtras().get("Persons");

ArrayAdapter adapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, i);

listView.setAdapter(adapter);
Create two classes that extend AsyncTask. One for each database call. I would not normally call AsyncTask for transactions I was not expecting anything back, so feel free to use standard Java Thread for the insert
new Thread(new Runnable() {
    @Override
    public void run() {
        Person person =new Person();
        person.setName(name);
        labDatabase.personDao().insertPerson(person);
    }
}) .start();
Instantiate a Database

In the MainActivity.java file, instantiate a LabDatabase object in the onCreate() as follows:
labDB = Room.databaseBuilder(this, LabDatabase.class, DATABASE_NAME)
                .build();
### NOTE: labDB will be declared outside of onCreate()

Perform the database functions in the doInBackground() methods as follows:

In the insert task:
database.personDao().insertPerson(person);
In the retrieval task:
ArrayList<String> personNames = new ArrayList<>();
for(Person p: persons) {
    personNames.add(p.getName());
}

Intent i = new Intent(context, PersonsActivity.class);
i.putExtra("Persons", personNames);
context.startActivity(i);
Test your code one final time.
Share, commit, and push lab to your GitHub account
