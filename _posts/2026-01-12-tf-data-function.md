---
title: 'TF Chapter 2: Terraform Data Types & Basic Functions'
updated: 2026-01-10 11:56:41Z
date: 2025-11-11 09:33:58Z
tags: terraform
categories: terraform
---

# Chapter 2: Terraform Data Types & Functions
## A. Data Types
To understand more about variables and input values you will be using Terraform, you will need to have some knowledges on the supported data types Terraform HCL has to offer. 

There are two major classification of data types, which is called primitive data types and complex data types. Primitive data types are the basic building type of all types that are not made from any other types, like string, boolean, and numbers. Complex data types are the data types that groups multiple values into a single value.

We will explore more on the various data types and their classifications below.

### string
A primitive data type designed to store a sequence of Unicode characters forming a text. Similar to how string works in a programming language, it's an easy way to represent texts value.

### number
A primitive data type designed to store numerical values. Unlike some other language, it can represent both float & integer data types without any necessity to switch data types.

### bool
A primitive data types designed to store booleans. Similar to other languages, it's meant to store True/False value.

### list/tuple & set
A complex data type that functions as an array. There are some distinct differences between list, tuple, and set which we will explore, but the structure is pretty much similar, defined in the form of braces (`[]`).

- **List**: Dynamic sized, ordered collection where every element must be of the same type. Accessible like a normal array using `list_name[index_number]` where `index_number` range is from `0 - (max_length-1)`. The syntax to define a list will be `list(type)`.
- **Tuple**: Fixed sized, ordered collection where elements can have different types. Similar to list, the elements within the tuple is accessible like a normal array using `tuple_name[index_number]` where `index_number` range is from `0 - (max_length-1)`. The syntax to define a tuple will be `tuple([type, type, ...])`. 
- **Set**: Dynamic sized but unordered collection with the same type. As this is unordered, it is not directly acccessible and requires the object in the set to be unique. This is frequently used to be the "key" for loops which will be explained later, but for now do note that set has its elements being unique as a feature. The syntax to define a set is `set(type)`

To visualize it easier, you can see from the following table for the differences.

| Feature | List | Tuple | Set |
| :--- | :--- | :--- | :--- |
| **Ordering** | **Ordered** (maintained by index) | **Ordered** (maintained by index) | **Unordered** |
| **Data Types** | **Homogenous** (all items must be the same type) | **Heterogenous** (can mix different types) | **Homogenous** (all items must be the same type) |
| **Uniqueness** | Allows duplicate values | Allows duplicate values | **Unique values only** (duplicates are removed) |
| **Syntax** | `["a", "b", "a"]` | `["a", 15, true]` | `["a", "b", "c"]` |
| **Type Definition** | `list(string)` | `tuple([string, number, bool])` | `set(string)` |
| **Common Use** | Passing multiple similar IDs (e.g., Subnets) | Returning a fixed record of mixed data | Ensuring unique entries (e.g., Security Group ports) |

In most cases, you will be playing more around **list** & **set** when developing a terraform code, but do note that **tuple** exist as a type.

### map/object
Another complex data type that functions as sort of json-like key-value type.

- **Map**: Dynamic sized key-value pair that requires all value to have the same type. This 
- **Object**: Fixed size key-value pair that has predefined list of key-value but does not enforces a certain type to be used on all the value. In a way object is sort of the tuple of map data type.

As per before, to see the differences between them in a better clarity, you can check the table below.

| Feature | Map | Object |
| :--- | :--- | :--- |
| **Data Types** | **Homogenous** (All values must be the same type) | **Heterogenous** (Each key can have a different type) |
| **Schema** | Flexible (Any number of keys allowed) | Strict (Must match the defined structure/schema) |
| **Syntax** | `map(string)` | `object({ name = string, age = number })` |
| **Key Access** | `var.my_map["key_name"]` | `var.my_obj.key_name` or `var.my_obj["key_name"]` |
| **Optional Keys** | Not applicable (all values are same type) | Supported via the `optional()` modifier |
| **Best Use Case** | When you have a list of similar items (e.g., tags, environment variables). | When you need a complex structure (e.g., a server config with a name, IP, and enabled-flag). |

Now that you have learn the various kinds of data types, you can now use it on your variables and inputs to improve your terraform code!

```
variable "instance_count" {
	type = number
	default = 0
}

variable "tags" {
	type = map(string)
	default = null
}

variable "az" {
	type = list(string)
	default = ["ap-southeast-3a", "ap-southeast-3b"]
}

variable "resource_name"{
	type = string
	default = null
}
```

Congratulation! Now you have successfully learnt about the various data types you can use in a variable!

## B. Basic Functions
Once you are starting to write your first terraform code, you might stumble into issues related to data types differences and such. This section will try to show the common functions often used in most of terraform code.
### Primitive type-related functions
Terraform has supported type changes across primitive data types within its limits. Booleans are able to switch type with string and vice-versa, with `True` and `False` just turning into `"True"` and `"False"` string. Numbers are also able to switch type into string, with number `10` becoming `"10"` string. Unlike some other languages where booleans can also be represented as 1 and 0, Terraform does not allow such interswitching as the boolean values are not internally represented by any numerical value.

- `tostring(input)`: Transform other primitive data type values into string. As stated before, booleans and numbers are able to be represented as a string with value `"True/False/<any number>"`, and will convert the input into the specified value.
- `tonumber(input)`: Transform string into number. The string value must represent a number or it will return an error.
- `tobool(input)`: Transform string into boolean. The string value must represent boolean value or it will return an error.

If you tried to insert a complex data type to these function, it will return an error. It can only convert type based on the specification of the function.

Null is also accepted as a valid input by these function, if you are curious. It will just return `null` in most cases.

Other than type changes, you can also do manipulation to your primitive data type values as well, mainly for number and string. Below are the most common functions used to manipulate the values passed in your variables or outputs

- `lower(string)`: Turn your string input's upper case letter into lower case. This is often used on S3 bucket naming where you can only put lower case into the name but you may have the variable set to upper case either by design or accidentally.
- `upper(string)`: The upper case counterpart of `lower(string)`, where it'll convert your lower case letter in your string into lower case.
- `regex(pattern, target_string)`: If you ever need to use regex to process a string, this is the function. It can be used to find and fetch certain values within the string that follows the pattern you provide on the function. The pattern follows general regex semantics, which will not be explored further in this module. You can explore more regarding regex on the documentation or other sources, but just note that terraform provide this capability as a language.

There are still more of functions related to numerical and string manipulation, but these are the most common ones used regularly in a generic terraform module.

### Complex data type-related functions
In general functions related to complex data types that are commonly used are for processing the values within the data types itself. Type conversion functions are only required when converting an object to a map as Terraform is already smart enough to process map as an object when required (not the case for object->map as the element's data types in an object can be heterogenous). Below are the common functions normally used to process complex data type values.

- `tomap(object)`: Automatically convert an object into a map, changing all the elements's data type to the most compatible primitive data type. If it only hold string/number/bool (one data type), it will keep the data types. If there are different data types within the elements, it will always try to convert to string as it is the most general data type to be converted.
- `concat(list1, list2, tuple3, ...)`: A function to merge two or more lists/tuples into one single list/tuple. Behaves similar to `tomap()` when it come to data types handling. Will try to switch the data type into tuple if trying to concatenate between list and tuple.
- `merge(map1, map2, object3, ...)`: The counterpart of `concat()` for map and object. Will try to switch the data type to object when trying to merge between map and object. Often used to merge tagging scheme but has other use case as well.
- `length(input)`: A function to find the length of a list/tuple, map/object, or string. 
- `lookup(map, key, default)`: The cleaner way to access elements within a map. If the key isn't found, it will return the default value set by the function. 
    > **Note:** While it works with objects, Terraform will implicitly convert the object into a map first. This can force all elements into a single data type, which might not be what you want. 
    > 
    > For objects, it is usually better to use **Dot Notation** (`var.object.key`). Since objects have defined schemas, an optional key will already return a value (`null` by default) rather than crashing the code.

---
There are still tons of functions that are not covered in this module so try to check on the following link below if you are curious.
[Functions - Hashicorp Terraform Language Documentation](https://developer.hashicorp.com/terraform/language/functions)