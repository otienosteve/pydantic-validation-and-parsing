# Pydantic validation and parsing   

### Into to validation  

```
from pydantic import BaseModel

class User(BaseModel):
    first_name: str
    last_name: str
    email: str
    password: str
    age: int

```


``` 
u1 = User(first_name="Ada", last_name="Lovelace", password="adalve123", age=34)
print(u1)
```
When we run the code we will get an Validation error for the email as below.    

![Email Validation Pydantic](./enail%20validation%20error%20Pydatic.png)    


### sources 

[Computer Hope- data Validation](https://www.computerhope.com/jargon/d/datavali.htm)   
[corporate Finance- Data Validation](https://corporatefinanceinstitute.com/resources/data-science/data-validation/)  
[Never trust a user- Laravel News](https://laravel-news.com/never-trust-your-users)     
[Pydantic Validators](https://docs.pydantic.dev/latest/usage/validators/)     
[Pydantic Version 1.10](https://docs.pydantic.dev/1.10/)       
[Pydantic Version 2.2](https://docs.pydantic.dev/latest/)   
[Validity -What-are-the-rules-for-email-address-syntax](https://knowledge.validity.com/hc/en-us/articles/220560587-What-are-the-rules-for-email-address-syntax-)    
[codurance- Password Validation Rules](https://www.codurance.com/katas/password-validation)         
