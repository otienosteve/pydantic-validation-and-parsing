# Pydantic validation and parsing   

### Into to validation  
Never Trust the user!- This is a golden rule in programming, though pessimistic its  a crucial factor to consider when developing software.   

Deviation alert! 🚧🚧🚧        

Ernest Gaines once wondered, “Why is it that, as a culture, we are more comfortable seeing two men holding guns than holding hands?” 
We human beings boast of how much we love peace, but how comes when watching an action movie we usually tend to be eager to get to the part where the drama starts, where two people are either pointing guns at each other or where theres suspense and and fists are clenched.   
     

The above short narrative tells you a lot about Human beings intrinsic nature, a pinch of salt kindness and a bag of rice malice. Does it have to do with the original sin? I am not a theologian but maybe. Anyway the point that am trying to drive home is that you never have full awareness of the intentions of whoever you are dealing with at any time. Don't mistake this for a campaign to proselytize you into the philosophy of always staying dangerous, but a call to be cautious if not mean with the level of trust you give.     

It should go without saying that our users should not be trusted, they may not be ill-intended but probably out of curiosity they may desire to stretch our application past it's limit. We as programmers therefore need to be creative enough to devise a way to tether them within certain confines when interacting with our application.   
Fortunately for us validation exists as a means to police input from users and so we do not need to re-invent the wheel.  

### Validation 
Validation is the process of passing data through certain checks to ensure it conforms to a particular required standard.     
There are a number of data validation types which can be broadly categorized into 6 distinct groups.   
1. Data Type Validation     
This type of validation ensures that the value supplied by the user is compatible with the type that is expected. As an example if number is required then anything that is not a number is disallowed. 
2. Range Type Validation    
This type of check ensures that the data entered lies within particular range (upper and lower limit) If the entered data is not within the specified range then it is discarded/ the system throws an exception.   
3. Format Check     
Ensures that the data submitted is in the format required. An example is the email format, it must have 4 parts: the name, the @ symbol, the domain and the top level domain.  
4. Consistency Validation     
This type of validation ensures that the data submitted is logically correct in comparison to other input. The Date of birth entered by the user shouldn't be a future date.    
5. Uniqueness Validation        
This validation ensures that no two entries for a particular field are the same like an email or id.    
6. Code Validation.     
Ensures that encoded data is valid according to the scheme specification.    

### Validation with pydantic  
Pydantic is a popular library in python that is useful for data validation. 
Pydantic shines because of it's speed, powered type hints and customization to mention but a few.   
Let us now explore how to use pydantic in validation.

There are a certain number of ways pydantic aims to achieve validation but these 3 are the most common.  
1. Constraining fields using the constrain methods applicable for the various data types available in python i.e `conint, constr, confloat, condecimal, conlist` etc.   
2. Adding validation metadata to the Field function. `Field()`    
3. Use of the validation function ` field_validator() `    

#### 1. Adding validation using constraining functions.   
These functions constrain data added to particular fields. Each type in python has a corresponding constrain function.    
The functions receive predefined keyword arguments used to enforce certain constrains to the fields.        
Below is a list of some of the functions and the corresponding keyword arguments.

- conint() - int
    - gt (value should be greater than) 
    - ge (value should be greater than or equal to)
    - lt (value should be less than)
    - le (value should be less than or equal to)
    - multiple_of (value should be a multiple of)
    - strict (whether to allow coercion from compatible types)      

example:    
    age: conint(ge=14,le=70)   

- confloat() - applicable to float data types  
    - gt (value should be greater than)
    - ge (value should be greater than or equal to)
    - lt (value should be less than)
    - le (value should be less than or equal to)
    - multiple_of (value should be multiple of)
    - strict (whether to allow coercion from compatible types)      

example:    
    margin: confloat(gt=0.1,lt=0.9,strict=True) 


- constr() - applicable to str data type    
    - strip_whitespace (strip whitespace from the string),
    - to_upper (convert the string to uppercase),
    - to_lower (convert the string to lowercase),
    - strict (whether to allow coercion from compatible types),
    - min_length (minimum length of the string.),
    - max_length (maximum length of the string.),
    - pattern (regex pattern that the string must match)       

example:    
    name: constr(min_length=5,max_length=20) 
     
- codecimal() - applicable to decimal data type 
    - gt (value should be greater than)
    - ge (value should be greater than or equal to)
    - lt (value should be less than)
    - le (value should be less than or equal to)
    - multiple_of (value should be multiple of)
    - strict (whether to allow coercion from compatible types) 
    - max_digits (maximum number of digits value should have)  
    - decimal_places (number of decimal places value should have)
    - allow_inf_nan (allow infinity and NaN)

example:    
    amount: condecimal(max_digits=5,decimal_places=2) 
     
- conlist() - applicable to the list data type  
    - item_type	 (type of the items in the list (required))
    - min_items (minimum length of items in the list)
    - max_items (maximum length of items in the list)
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
      
Apart from these there are other types that can be constrained e.g  conbytes()-bytes, conset()-set, confrozenset() - frozenset.  More details on [The pydantic types official documentation API](https://docs.pydantic.dev/latest/api/types/#pydantic.types)

#### The code below demonstrates how we can achieve validation with the use of the constrained fields.   

```
from pydantic import conint, constr, conlist, condecimal

from datetime import date, timedelta

class Example(BaseModel):
    age: conint(ge=14,le=70) 
    margin: confloat(gt=0.1,lt=0.9,strict=True)
    name: constr(min_length=5,max_length=20)
    amount: condecimal(max_digits=5,decimal_places=2)
    shopping_list: conlist(str,min_items=2,max_items=10)
    date: condate(ge=date.today(),le=date.today()+timedelta(days=5))
```  

#### 2. Adding validation using the Field function  
The Field function is used to add metadata to the fields defined in the model.  
The metadata may include keyword arguments for constraining fields (gt,le,min_length,decimal_places etc ) similar to the constraining fields we had seen earlier. 
    
Below is a list of keyword arguments the function accepts. 

default -  value if the field is not set    
default_factory - callable used to generate values  
validate_default - specifies wether the default field should be validated.     
alias - an alternative name for the attribute   
exclude - determines wether to exclude the field from the model      
strict - if True the Validation will be applied strictly    
gt - greater than   
ge - greater than or equal to       
lt - less than  
le - less than or equal to      
multiple_of - values must be a multiple of  
min_length - must not be less than that length  
max_length - must not be more than length   
max_digits - maximum number of digits allowed       
decimal_places - maximum number of decimal places allowed   
extra -  include extra fields used by json      

Note: The `Field()` function  is versatile but should be used with caution. The validation you add to the field should be compatible with the type it's being added for. i/e for a int type you can use gt but not max_length, for the decimal type you can use decimal_places but not min_length. 
    
Example usage:      
      
```
age: Field(strict=True,gt=14,le=70, )  # int type
name: Field(min_length=5,max_length=20)  # str type
amount: Field(max_digits=5,decimal_places=2) # decimal type
```
#### 3. Adding Validation using the field_validator function. field_validator()    
This validation technique gives us more freedom to perform not only validation but also parsing. The validation function should do one of two things: **either throw a validation/Value error or return the parsed value.**   
To use it we create a function that is used to validate a particular field and decorate it with the field validator function.  
Below is an example syntax.      
```
from pydantic import BaseModel, field_validator 

class Example(BaseModel):
    name : str 
    age : int
    email : str

    @field_validator('name')
    @classmethod
    def validate_name(cls, value):
        pass
```  
The `field_validator()` function accepts a field to validate as an argument. It can also accepts multiple or all fields as an arguments:      
`@field_validator('name')`  - selecting a single field     
`@field_validator('name','age')`  - selecting multiple fields    
`@field_validator('*')` - selecting all fields    

Note: The field validator is a class method and therefore the first argument it receives should be the class.      
The decorated function that is used for validation accepts the arguments in the following order.    
1. The class - cls
2. The field value - value  
3. The FieldValidationInfo - Will contain data about the current validation context such as fields already validated (FieldValidationInfo.data) and the current field (FieldValidationInfo.field_name)  

Example:
```
    @field_validator('email')
    @classmethod
    def validate_email(cls, value, info:FieldValidationInfo):
        # cls - the class
        # value - the field value
        # info - field validation info 
        pass
``` 
#### Parsing 
Parsing is the process of cleaning data (sanitizing) so that it matches a set format. An ideal example would be; for a first_name field we would probably receive a correct value `allan` ,`aLLan`, `allaN` or `ALLAN` but it's format doesn't match the required standard, we probably want to title case it to `Allan`. The process of elevating the value to the standard we require is what we call validation. 

Another example would be to receive the firstname and convert it to all lowercase as depicted below.       
```
    @field_validator('first_name')
    @classmethod
    def validate_name(cls, value, info:FieldValidationInfo):
    # an example of parsing
        return value.title()
```     
Whatever is returned from the validation function will be the value for the field, we can therefore take advantage of this and parse the value as we deem fit, before returning it.   
The steps involved in using the field_validator() are ascertaining validity of the data and if it passes all checks then we parse it if necessary for it to match the standard our system desires. 

Validation and Parsing Example.     

```
from pydantic import BaseModel,FieldValidationInfo,ValidationError,field_validator,
import re

class Example(BaseModel):
    first_name : str 
    email : str

    @field_validator('first_name')
    @classmethod
    def validate_name(cls, value):
        # confirm if the first name value contains a number
        if re.findall(r'\d',value):
            raise ValidationError('first name must not contain a number')
        # confirm if the first name value contains a special character
        if re.findall(r'\W',value):
            raise ValidationError('first name must not contain special character')
        return value.title()
        

    @field_validator('email')
    @classmethod
    def validate_email(cls,value, info:FieldValidationInfo):
     #regex pattern to match for email
      mailregex = re.compile(r"([-!#-'*+/-9=?A-Z^-~]+(\.[-!#-'*+/-9=?A-Z^-~]+)*|\"([]!#-[^-~ \t]|(\\[\t -~]))+\")@([-!#-'*+/-9=?A-Z^-~]+(\.[-!#-'*+/-9=?A-Z^-~]+)*|\[[\t -Z^-~]*])")
      # check if email entered matches pattern above
      if re.fullmatch(mailregex,value):
         return value.lower()
      raise ValidationError("Error in the email entered")
```     
In the Code above we have demonstrated how to use the field validator method to validate fields. If the field satisfies the requirement we parse as we have done changing the email to lowercase and first name to title case.  
Note: If the Validation function does not return a value then it will be set to None irrespective of wether the correct value was provided or not.  

In the next lesson we will have a code along where we will walk through how to perform validation with pydantic.  

### sources 

[Computer Hope- data Validation](https://www.computerhope.com/jargon/d/datavali.htm)   
[corporate Finance- Data Validation](https://corporatefinanceinstitute.com/resources/data-science/data-validation/)  
[Never trust a user- Laravel News](https://laravel-news.com/never-trust-your-users)     
[Pydantic Validators](https://docs.pydantic.dev/latest/usage/validators/)     
[Pydantic Version 1.10](https://docs.pydantic.dev/1.10/)       
[Pydantic Version 2.2](https://docs.pydantic.dev/latest/)      
[codurance- Password Validation Rules](https://www.codurance.com/katas/password-validation)    
[Pydantic types](https://docs.pydantic.dev/latest/api/types/#pydantic.types)  
[Field Pydantic](https://docs.pydantic.dev/latest/usage/fields/)    
[Field API Documentation](https://docs.pydantic.dev/latest/api/fields/)    
[Stackabuse Email Regex For Validation](https://stackabuse.com/python-validate-email-address-with-regular-expressions-regex/)    
  
