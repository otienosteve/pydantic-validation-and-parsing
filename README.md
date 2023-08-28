# Pydantic validation and parsing   

### Into to validation  
This is the process of passing data through certain checks to ensure it conforms to a particular required standard.     
There are quite a number of data validation types but they can be broadly categorized into 6 distinct groups.   
1. Data Type Validation
This type of validation ensures that the data type entered is compatible with the type required. As an example if int data is required then all other incompatible types are disallowed from entering the system or discarded. 
2. Range Type Validation 
This type of check ensures that the data entered lies within particular range (upper and lower limit) If the entered data is not within the specified range then it is discarded/ the system throws an error.   
3. Format Check 
Ensures that the data submitted is in the format required. An example is an email must be of a specific format, it must have 4 parts: the name, the @ symbol, the domain and the top level domain.  
4. Consistency Validation   
This type of validation ensures that the data submitted is logically correct in comparison to other input. the Date of Birth entered by the user shouldn't be a future date.    
5. Uniqueness Validation    
This validation ensures that no two entries for a particular filed are the same like an email or id.    
6. Code Validation. 
Ensures that encoded data is valid according to the code specification.    

### Validation with pydantic    

Never Trust the user!- This is a golden rule in programming. It may sound pessimistic but it'a crucial factor to consider when developing software for end users, afterall even a black hat hacker can be a user who is busy seeking to exploit your any tiny vulnerability in your system.     

Let us now explore how pydantic achieves validation.    
There are 3 distinct ways pydantic aims to achieve validation.  
1. constraining fields using the constrain methods for the different types i.e `conint, constr, confloat, condecimal, conlist` etc.   
2. Adding validation metadata to the Field function. `Field()`    
3. Use of the validation function ` field_validator() `  

To demonstrate the use of the various ways of validation we will use a user profile model with the fields first_name, last_name, email, password and age.   

```
from pydantic import BaseModel

class User(BaseModel):
    first_name: str
    last_name: str
    email: str
    password: str
    age: int

```
Our aim is to add the following validation rules to the different fields.     
- first_name and last_name  
     - must not be empty.   
     - must be at least 2 characters and at most 20 characters.   
     - must not contain numbers or special characters.  
- email     
    - Must contain the 4 most essential parts  
        - recipient name (alphanumeric and special chars allowed, special chars must not be first or last)
        - the @ symbol
        - domain name (alphanumeric characters, maybe contain a hyphen and a period , )
        - th period (.) 
        - Top level domain (letters) 
- Password  
        - have more than 8 characters   
        - contain a capital letter   
        - contains lowercase letters    
        - contain a special character.   
- Age   
        - must be an integer.   
        - must not be less than 14.     
        - must not be greater than 70.   

    
Before we add validation to the fields above we will start by making deliberate mistakes to see how pydantic behaves by default by not setting some fields and providing wrong types to some fields.    
Create an instance of the model above and intentionally exclude the email field. 
```    
u1 = User(first_name="Ada", last_name="Lovelace", password="adalve123", age=34)
print(u1)
```
When we run the code we will get a validation error for the email as below.    

![Email Validation Pydantic](./enail%20validation%20error%20Pydatic.png)    
To avoid getting this error we can add a default value to the field this way.   
`email : str ='email@domain.com' `  
When we run our code again you will notice that now we do not get an error and the email field is automatically populated to the default we have set. Now of course you may be right if you have judged that this is not the best approach to handling emails and probably any other fields. A better approach would be to make the values None by default for most use cases and provide the necessary logic for handling unset values for the functionality you would like to achieve.    
Example:
```
class User(BaseModel):
    first_name: str = None
    last_name: str =None
    email: str = None
    password: str = None
    age: int = None
```
Another more explicit way would be to make use of the Optional attribute from the typing module.  
` from typing import Optional `     
```
class User(BaseModel):
    first_name: Optional[str] = None
    last_name: Optional[str] = None 
    email: Optional[str] = None
    password: Optional[str] = None
    age: Optional[int] = None
```     
The next intentional mistake we want to make is add invalid data types to some of our fields.    
Adding a string to the age field that requires an int.    
```
u1 = User(first_name="Ada", last_name="Lovelace",email="adalve@gmail.com",password="adalve123", age='34')
print(u1)
```     
On running our code we get do not get an error as the value we provide is automatically coerced into an integer. This is never always the case as it will depend on wether the value can be coerced into the required type.     
The code below would throw an error.      
```
u1 = User(first_name="Ada", last_name="Lovelace",email="adalve@gmail.com", password="adalve123", age='f34')
print(u1)
```     
![Error for incompatible type](./Error%20for%20invalid%20input-Pydantic.png)    
The same exception would be thrown if an str field receives an int. You can further  explore with different incompatible types to be more informed on how coercion works by default in pydantic.    

#### 1. Adding validation using constraining functions.   
These functions are used to constrain data that can be added to particular fields. Each type in python has a a corresponding constrain function.    
The functions receive predefined keyword arguments used to add enforce certain constrains to the fields.        

- conint() - int
    - gt (greater than)
    - ge (greater than or equal to)
    - lt (less than)
    - le (less than or equal to)
    - multiple_of (multiple of)
    - strict (whether to allow coercion from compatible types)      

example:    
    age: conint(ge=14,le=70)          
- confloat() - float  
    - gt (greater than)
    - ge (greater than or equal to)
    - lt (less than)
    - le (less than or equal to)
    - multiple_of (multiple of)
    - strict (whether to allow coercion from compatible types)      

example:    
    margin: confloat(gt=0.1,lt=0.9,strict=True) 


- constr() - str    
    - strip_whitespace (strip whitespace from the string),
    - to_upper (convert the string to uppercase),
    - to_lower (convert the string to lowercase),
    - strict (whether to allow coercion from compatible types),
    - min_length (minimum length of the string.),
    - max_length (maximum length of the string.),
    - pattern (regex pattern that the string must match)       

example:    
    name: constr(min_length=5,max_length=20) 
     
- codecimal() - decimal 
    - gt (greater than)
    - ge (greater than or equal to)
    - lt (less than)
    - le (less than or equal to)
    - multiple_of (multiple of)
    - strict (whether to allow coercion from compatible types) 
    - max_digits (maximum number of digits)  
    - decimal_places (number of decimal places)
    - allow_inf_nan (allow infinity and NaN)

example:    
    amount: condecimal(max_digits=5,decimal_places=2) 
     
- conlist() - list  
    - item_type	 (type of the items in the list (required))
    - min_items (minimum length of the list)
    - max_items (maximum length of the list)
    - unique_items (whether the items in the list must be unique)

example:    
    shopping_list: conlist(str,min_items=2,max_items=10) 
     
- condate() - date   
    - gt (the value must greater than)
    - ge (the value must greater than or equal to)
    - lt (the value must less than)
    - le (the value must less than or equal to)
    - strict (whether to validate the date value in strict mode)   

example:    
    date: condate(ge=date.today(),le=date.today()+timedelta(days=5))
      
Apart from these there are other types that can be constrained, example conbytes()-bytes, conset()-set, confrozenset() - frozenset.  more details on [The official documentation API](https://docs.pydantic.dev/latest/api/types/#pydantic.types)

#### The code below demonstrates how we can achieve validation with the use of the constrained fields.   

```
from pydantic import conint, constr,conlist,condecimal

from datetime import date, timedelta

class Example(BaseModel):
    age: conint(ge=14,le=70) 
    margin: confloat(gt=0.1,lt=0.9,strict=True)
    name: constr(min_length=5,max_length=20)
    amount: condecimal(max_digits=5,decimal_places=2)
    shopping_list: conlist(str,min_items=2,max_items=10)
    date: condate(ge=date.today(),le=date.today()+timedelta(days=5))
```  

#### Adding validation using the Field function  
The Field function is used to add metadata to the field defined in the models. The metadata may include keyword arguments for constraining fields 




### sources 

[Computer Hope- data Validation](https://www.computerhope.com/jargon/d/datavali.htm)   
[corporate Finance- Data Validation](https://corporatefinanceinstitute.com/resources/data-science/data-validation/)  
[Never trust a user- Laravel News](https://laravel-news.com/never-trust-your-users)     
[Pydantic Validators](https://docs.pydantic.dev/latest/usage/validators/)     
[Pydantic Version 1.10](https://docs.pydantic.dev/1.10/)       
[Pydantic Version 2.2](https://docs.pydantic.dev/latest/)   
[Validity -What-are-the-rules-for-email-address-syntax](https://knowledge.validity.com/hc/en-us/articles/220560587-What-are-the-rules-for-email-address-syntax-)    
[codurance- Password Validation Rules](https://www.codurance.com/katas/password-validation)    
[Pydantic types](https://docs.pydantic.dev/latest/api/types/#pydantic.types)  
[Field Pydantic](https://docs.pydantic.dev/latest/usage/fields/)    
[Field API Documentation](https://docs.pydantic.dev/latest/api/fields/)      
