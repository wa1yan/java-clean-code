# Robert C. Martin's Clean Code
### This is learning to book of Robert C. Martin's clean code.

## Table of Contents  
  [Function](#headers)  
  [Comments](#comment)  
  [Object And DataStructure](#obj&DS)
  [Excetpions](#exceptions)
.   
<a name="headers"/>
## Function

Small
> The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.
> Very small functions are better.
> Functions should not be 100 lines long. Functions should hardly ever be 20 lines long. 

Do one things

    FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY

Switch Statement

> It’s hard to make a small switch statement. Even a switch statement with only two cases is larger than I’d like a single block or function to be.
> Switch statment do N things.

Use descriptive names
> It better describes what the function does.
> You know you are working on clean code when each routine turns out to be pretty much what you expected.
> Principle is choosing good names for small functions that do one thing.
> Don’t be afraid to make a name long. A long descriptive name is better than a short enigmatic name. A long descriptive name is better than a long descriptive comment.
      
     Don’t be afraid to spend time choosing a name.

Functions Arguments 
* If there are no arguments, this is trivial.
* If there’s one argument, it’s not too hard.
* With two arguments the problem gets a bit more challenging.
* With more than two arguments, testing every combination of appropriate values can be daunting.

### One Arguments
> There are two very common reasons to pass a single argument into a function
* Transforming it into something else and returning it.

       boolean fileExists(“MyFile”);
       InputStream fileOpen(“MyFile”);

### Flag argument
> Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice.
       
       Bad:  
       
       fileExistAfterDeleted("C://Folder", "test.pdf", true); // we don't know 'true' is what
       
       public boolean fileExistAfterDeleted(String folderPath, String fileName, boolean isDelete) {
		
		File file = Paths.get(folderPath, fileName).toFile();	
		if(isDelete) {
			if(file.delete()) {
				return false; // since file is not exist
			}
		}
		return file.exists();
	}
	
	Refactor: 
	
	fileExistAfterDeleted("C://Folder", "test.pdf");
	
	public boolean fileExistAfterDeleted(String folderPath, String fileName) {
		return fileExistAfterDeleted(folderPath, fileName, true);
	}
	
       	private boolean fileExistAfterDeleted(String folderPath, String fileName, boolean isDelete) { //private method
		
		File file = Paths.get(folderPath, fileName).toFile();	
		if(isDelete) {
			if(file.delete()) {
				return false; // since file is not exist
			}
		}
		return file.exists();
	}
      
### Two Arguements 
> Two argument is hardly to understand than one but we will certainly have to write them.


### Three Arguments
> Three arguments are significantly harder to understand than two. Very careful to create with three arguments.

### Arguement Object
> When a function seems to need more than two or three arguments, we can wrapped as a object.

      Circle makeCircle(double x, double y, double radius);
      Circle makeCircle(Point center, double radius);
   
### Verb and Keywords
 Choose good names for a function with verb/noun pair.

> boolean delete(File file)

> boolean isSuccessDelete(File file)

isSuccessDelete(..) is more suitable than delete(..)

### Have No Side Effects
The following code has side effect in isExistFile method. This side effect create a temporary coupling with isExistFile method so we should make it clear in the name of the function `checkPdfFile(String folderPath, String fileName)` to  `checkPdfFileAndExist(String folderPath, String fileName)`.

      public boolean checkPdfFile(String folderPath, String fileName) { //should change method name coz of coupling
		    boolean isExist = false;
		    File file = Paths.get(folderPath, fileName).toFile();	
		    if(isPdfFile(file)) {
			    if(isExistFile(file)) { //side effect
				    isExist = true;
			    }
		    }
		    return isExist;
	    }
      
      public boolean isExistFile(File file) {
		    return file.exists();
	    }
      
### Common Query Separation

      public boolean canUseTobacco(int age) {
		    if(age >= 21 ) { // can have many condition to use tobacoo
			    return true;
		    }
		    return false;
	    }
      
The above code is need to separate the command from the query.

      public boolean canUseTobacco(int age) {
		    if(isOver20(age)) { // query separation
			    return true;
		    }
		    return false;
	    }
      
      public boolean isOver20(int age) {
		    return age > 20;
	    }

### Extract Try/Catch Blocks
> The deletePageAndAllReferences function is all about the processes of fully deleting a page. Error handling can be ignored. This provides a nice separation that makes the code easier to understand and modify.

      public void delete(Page page) {
       try {
          deletePageAndAllReferences(page);
        }
        catch (Exception e) {
          logError(e);
        }
       }
       
       private void deletePageAndAllReferences(Page page) throws Exception {
          deletePage(page);
          registry.deleteReference(page.name);
          configKeys.deleteKey(page.name.makeKey());
       }
       
       private void logError(Exception e) {
          logger.log(e.getMessage());
       }
       
 ### Don't Repeat Yourself
 > Avoid duplicate code for doing onething with many condition

.....  
<a name="comment"/>
## Comments

.....
<a name="obj&DS"/>
## Object And Data Structure
> Objects expose behavior and hide data. This makes it easy to add new kinds of objects without changing existing behaviors
### The Law of Demeter (Principle of least knowledge)

According to the Law of Demeter, a method M of object O should only call the following types of methods:
Methods of Object O itself
Methods of Object passed as an argument
Method of an object, which is held in an instance variable
Any Object which is created locally in method M

>  A.getObjectB().getObjectC().display() // this is a violation of Law of Demeter.

### Train Wrecks

> Options opts = ctxt.getOptions();
> File scratchDir = opts.getScratchDir();
> final String outputDir = scratchDir.getAbsolutePath();

If options and scratchDir are just data structure with no behaviour so this is not apply demeter.

> final String outputDir = ctxt.options.scratchDir.absolutePath;

### Method Hiding And Method Overriding

> static method cann't override.


		public class Parent {

			public static void method1() { //static method coz of method hiding
				System.out.println("Method 1 of the Parent class is executed.");
			}

			public void method2() {
				System.out.println("Method 2 of the Parent class is executed.");
			}
		}
		public class Child extends Parent {

			public static void method1() { //method hiding with same signature
				System.out.println("Method-1 of the Child class is executed.");
			}

			public void method2() {
				System.out.println("Method-2 of the Child class is executed.");
			}

			public static void main(String args[]) {
				Parent parent1 = new Parent();
				Parent parent2 = new Child();

				parent1.method1();

				// parent2 is calling child's method1 but will do parent's method1 coz of method hiding
				parent2.method1(); 

				parent1.method2();
				parent2.method2();

				new Child().method1();
			}
		}

### Hiding Structure

### Data Transfer Object And Active Record

> we should use dto or record class to seperate sample POJO class and business object class.


<a name="exceptions"/>
## Error Handling

### Use excetpions rather than return codes

> public class DeviceController {
 ...
 public void sendShutDown() {
 DeviceHandle handle = getHandle(DEV1);
 // Check the state of the device
 if (handle != DeviceHandle.INVALID) {
 // Save the device status to the record field
 retrieveDeviceRecord(handle);
 // If not suspended, shut down
 if (record.getStatus() != DEVICE_SUSPENDED) {
 pauseDevice(handle);
 clearDeviceWorkQueue(handle);
 closeDevice(handle);
 } else {
 logger.log("Device suspended. Unable to shut down");
 }
 } else {
 logger.log("Invalid handle for: " + DEV1.toString());
 }
 }
 ...
}















