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
        - must not be greater than 18 characters.   

    
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
Another way would be to make the fields optional.  
` from typing import Optional `     
```
class User(BaseModel):
    first_name: Optional[str] 
    last_name: Optional[str] 
    email: Optional[str] 
    password: Optional[str] 
    age: Optional[int] 
```     



### sources 

[Computer Hope- data Validation](https://www.computerhope.com/jargon/d/datavali.htm)   
[corporate Finance- Data Validation](https://corporatefinanceinstitute.com/resources/data-science/data-validation/)  
[Never trust a user- Laravel News](https://laravel-news.com/never-trust-your-users)     
[Pydantic Validators](https://docs.pydantic.dev/latest/usage/validators/)     
[Pydantic Version 1.10](https://docs.pydantic.dev/1.10/)       
[Pydantic Version 2.2](https://docs.pydantic.dev/latest/)   
[Validity -What-are-the-rules-for-email-address-syntax](https://knowledge.validity.com/hc/en-us/articles/220560587-What-are-the-rules-for-email-address-syntax-)    
[codurance- Password Validation Rules](https://www.codurance.com/katas/password-validation)         
