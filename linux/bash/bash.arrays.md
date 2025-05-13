# LINKS

- https://mywiki.wooledge.org/BashGuide/Arrays
- https://www.mybluelinux.com/bash-guide-to-bash-arrays

# DECLARE

```sh
declare -a <name> # indexed array
declare -A <name> # associative array
```

```sh
limits=( 10 20 26 39 48)
array=("element1" "element2" "element3") # seperator = whitespace!

declare -a var or array=(…) # array
declare -A var # associative array
declare -r var # readonly; var can no longer be modified or unset
declare -x var # export; var is marked for export which means that it will be inherited by any child process
declare -i var # integer; assigning values to this variable automativally triggers arithmethic expansion

a=5+2; echo "$a" # => 5+2
declare -i a=5+2; echo "$a" # => 7
declare -p # display the attributes and value of each NAME
```

# ASSIGN

```sh
array=("element1" "element2" "element3") # seperator = whitespace!
array[0]="element1" # assignment by index; starts at 0!
```

## one array to another

```sh
array1=("element1" "element2" "element3")
array2=("${array1[@]}")
echo "array2 length: ${#array2[@]}"
echo "array2: ${array2[@]}"
```

## mapfile

- Read lines from stdin into the indexed array variable array
- or from file descriptor fd if the `-u` option is supplied
- The variable **MAPFILE** is the default array.  

```sh
-d <delim> # The first character of delim is used to terminate each input line, rather than newline. If delim is the empty string, mapfile will terminate a line when it reads a NUL character.
-n <count> # Copy at most count lines.
-n 0 # all lines are copied.
-O <origin> # Begin assigning to array at index <origin>. 
-O 0 # default start index
-s <count> # Discard the first <count> lines read
-t <delim> # Remove a trailing <delim> (default newline) from each line read.
-u <fd> Read lines from file descriptor fd instead of stdin
```
## add

```sh
# [add on top of the array]

array+=('value1') # adds value1 to array assigning to the highest index + 1
array+=("value2") # adds value2 to array assigning to the highest index + 1
array2+=("${array1[*]}") # adds all elements of array2 assigning them to the highest index + 1
```

# ACCESS / EXPAND

```sh
# curly braces ${} are required to avoid shell’s filename expansion operators

echo ${#array[@]} # prints the length of the array

echo "${array[@]}" # prints all elements of array each quoted separately (just if quoted!)
echo "${array[*]}" # prints all elements of array as one single quoted string (just if quoted!)

echo "${array[2]}" prints element3 
echo "${array[-1]}" prints last element
echo ${!array[@]} and ${!array[*]} # prints array indices
```

## get subset

```bash
#!/bin/bash

array=(1 2 3 4 5)

echo ${array[@]:2:3}
# output: 3 4 5

echo ${array[@]:1}
# output: 2 3 4 5

echo ${array[@]::2}
# output: 1 2

new_array=( ${array[@]::2} )  # create new array from old array
echo ${new_array[@]::2}
# output: 1 2
```
# ITERATE

## iterate over keys

```sh
for key in "${!fruits[@]}"
do
  echo "Key for fruits array is: $key"
done
```

```sh
declare -a active_employees=("John" "Raymon" "Irene" "Peter")

for index in "${!active_employees[@]}"; do 
    echo "active_employees[$index]=${active_employees[index]}"; 
done
```

## iterate over values

```sh
for item in ${array[@]}; do
	echo "$item"
done

# quote if the are values separated by space!
for item in "${commands[@]}"; do
	echo $item
done
```



```sh
# c-style loop
array=(apple orange grape)
for (( i = 0; i < ${#array[@]}; i++ )); do
  echo "The iteration number is $i"
  echo "The iteration item is ${array[$i]}"
done	
```

```sh
# [while loop]
# just for indexed (no associative) arrays

i=0
while [[ $i -lt ${#array[@]} ]]; do
	echo "${array[$i]}"
	i=$((i+1))
done

i=0
while (( $i < ${#array[@]} )); do
	echo "${array[$i]}"
	((i++)
done
```

# REMOVE

## unset

* the **remaining elements are left at their original positions**
* the remaining array is sparse!

```sh
unset array # destroy, delete, or unset
unset array[-1] # last item
```

### zsh

```zsh
# items are not shifted:
declare -a array=("Panda" "Owl" "Cat" "Rabbit" "Bear")
echo "${#array[@]}" # 5
unset array[2] # destroy, delete, or unset a single array element
echo "${#array[@]}" # 5 --> items are not shifted!
```

restore consecutive indexes

```bash
declare -a arr1=(foo '' bar '') # sparse array
declare -a arr2=()
for item in "${arr1[@]}"; do
    if [[ -n $item ]]; then 
        arr2+=( "$item" )
    fi
done

function removeEmptyFromArray() {
    local -n _sparseArray=$1
    local -n _cleanArray=$2
    
    for item in "${_sparseArray[@]}"; do
        if [[ -n $item ]]; then 
            _cleanArray+=( "$item" )
        fi
    done
}
```
## remove items from array

```bash
#!/bin/bash

array=("a" "b" "c" "a" "aa")
del="a"

for index in "${!array[@]}"; do
    if [[ ${array[$index]} == $del ]]; then
        unset 'array[index]'
    fi
done
echo ${array[@]}

# output:
b c aa
```
## remove items of one array from another array

```bash
array=("a" "b" "c" "a" "aa")
del_a=("a" "g" "b")

for index in "${!array[@]}"; do
    for del in "${del_a[@]}"; do
        if [[ ${array[$index]} == $del ]]; then
            unset 'array[index]'
        fi
    done
done
echo ${array[@]}

#output:
c aa
```
# TEST

### is not zero

```sh
function isArrayNotEmpty() {
  # use:
  # if isArrayNotEmpty myArray; then
  
  local -n arrayRef=$1  # Use nameref to pass array by reference
  if [[ ${#arrayRef[@]} -gt 0 ]]; then
      return 0  # Array is not empty (success)
  else
      return 1  # Array is empty (failure)
  fi
}
```

```sh
if [[ ${#myArray[@]} -gt 0 ]]; then
  echo "Array has elements" 
else 
  echo "Array is empty" 
fi
```

```sh
if (( ${#myArray[@]} )); then
  echo "Array has elements" 
fi
```

* Works because `((expression))` treats non-zero values as `true`.
## contains value
### false positives

Most methods not only match the entire word for a pattern, but **they can also match with a _subset_ of a string**. And then give a "pseudo success indication" that the value you are looking for is present, but in reality, it is not.

```bash
#/usr/bin/env bash

array=('hello' 'world' 'randomwords1000101sandwhich')

if [[ "${array[@]}" =~ '1000101' ]]; then
    echo "The number we were looking for, is present"
fi
```

### exact match

```sh
#!/usr/bin/env bash 
array=("value one" "value two" "value three") 
search_value="value two" 
if printf '%s\n' "${array[@]}" | grep -Fxq "$search_value"; then
    echo "Found: $search_value" 
else
  echo "Not Found: $search_value" 
fi
```

- `printf '%s\n' "${array[@]}"`
    
    - Prints each element of the array on a new line, ensuring proper handling of values with spaces.
    
- `grep -Fxq "$search_value"`
    
    - `-F`: Matches the string literally (disables regex).
    - `-x`: Ensures that only exact matches are considered (avoids partial matches).
    - `-q`: Suppresses output, making it suitable for conditional checks.

- Adding `--` prevents accidental parsing of `$search_value` as an option.

### function isValueInArray

```sh
function isValueInArray() {
  
  local _value="$1"
  local -n _array=$2
  
  for item in "${_array[@]}"; do
    echo "Check $item"
    if [[ "$item" == "$_value" ]]; then
      return 0 # Found
    fi
  done
  return 1 # Not found
}

# use
declare -a myArray=(8080 443 222 1289 98344)
if isValueInArray "80" myArray; then
  echo "YES"
else
  echo "NO"
fi
```

### function isKeyInMap

- does not work in zsh

```bash
declare -A myMap
myMap[8080]=1
myMap[132]=1
myMap[53]=1

[[ -v myMap[80] ]] && echo YES
[[ -v myMap[8080] ]] && echo YES
```

```sh
function isKeyInMap() {
  
  local _value="$1"
  local -n _array=$2
  
  for key in "${!_array[@]}"; do
    # echo "Check $key"
    if [[ "$key" == "$_value" ]]; then
      return 0 # Found
    fi
  done
  return 1 # Not found
}

# use
declare -A myMap
myMap[8080]=1
myMap[132]=1
myMap[53]=1

if isKeyInMap "8080" myMap; then
  echo "YES"
else
  echo "NO"
fi
```
# MERGE

```bash
#!/bin/bash

array_one=("a" "b" "c" "d")
array_two=("1" "2" "3" "4")
array=("${array_one[@]}" "${array_two[@]}")
echo ${array[@]}
echo ${#array[@]}

# output:
a b c d 1 2 3 4
8
```

# FILTER

```sh
# filter with grep
array=(./*.jpg)
for i in ${array[@]}; do 
	echo $i
done | grep [a-zA-Z].lib

# you can even create a new (filtered) array this way:
array2=( $( for i in ${array[@]} ; do echo $i ; done | grep [a-zA-Z0-9].lib ) )
```

```sh
# filter allFiles
for i in "${!allFiles[@]}"; do
	for j in "${!includes[@]}"; do
		if [[ ${allFiles[$i]} == *${includes[$j]}* ]]; then
			echo "${allFiles[$i]} == *${includes[$j]}*"
			files+=(${allFiles[$i]})
		fi
	done
done
```



```sh
for index in "${!ARR[@]}"; do 
	[[ …condition… ]] && unset -v 'ARR[$index]'; 
done
ARR=("${ARR[@]}") # needed if you want to have consecutive indexes
```

1. `"${!ARR[@]}"` evaluates to indexes of the array (as opposed to elements).
2. The form `"${!ARR[@]}"` is a must. You must not skip quotes or change `@` to `*`. Or else the expression will break on associative arrays where keys contain spaces (for example).
3. The part after `do` can be whatever you want. The idea is only that you must do `unset` as shown for the elements that you don't want to have in the array.
4. [It is advised or even needed](http://wiki.bash-hackers.org/syntax/arrays#destruction) to use `-v` and quotes with `unset` or else bad things may happen.
5. If the part after `do` is as suggested above, you can use either `&&` or `||` to filter out the elements that either pass or fail the condition.
6. The second line, reassignment of `ARR`, is needed only with non-associative arrays and **will break with associative arrays**. (I didn't quickly came out with a generic expression that will handle  both while I don't need one…). For ordinary arrays it is needed if you want to have consecutive indexes. Because `unset` on an array element does not modify (drop by one) elements of higher indexes - it  just makes a hole in the indexes. Now if you only iterate over the array (or expand it as a whole) this makes no problem. But for other cases  you need to reassign indexes. Note also that if you had any hole in the indexes before it will be removed as well. So if you need to preserve existing holes more logic has to be done beside the `unset` and final reassignment.

# ASSOCIATIVE ARRAYS

- https://mywiki.wooledge.org/BashGuide/Arrays#Associative_Arrays-1

## declare

```sh
# associative arrays
declare -A fruits

fruits[south]="Banana"
fruits[north]="Orange"
fruits[west]="Passion Fruit"
fruits[east]="Pineapple"
```

```bash
declare -A myArray=(
    [1]=one
    [2]=two
)
```
## access values

```bash
declare -A myArray
myArray["key1"]="value1"
myArray["key2"]="value2"

for value in ${myArray[@]}; do 
    echo "value: ${value}"
done
# value2
# value1

echo ${#myArray[@]} # 2
echo ${myArray["key1"]} # value1
echo ${myArray[key1]} # value1
```

## access key/value

```bash
declare -A myArray
myArray["key1"]="value1"
myArray["key2"]="value2"

for key in ${!myArray[@]}; do 
    echo "key: $key"
    echo "value: ${myArray[$key]}"
done
# key2
# key1
```

## multi-level

While Bash does not support nested, multi-level hashes, it is possible to emulate them with some auxiliary arrays. 

```bash
# not possible
declare -A args
args["readFileToArray"]=(
    ["file"]="${FILES[sitesDirs]}"
    ["ignoreComments"]=true 
)
# bash: args["readFileToArray"]: cannot assign list to array member
```

### ex: function args

```bash
declare -A ARGS

function addArgToFctn() {
    local -n args=ARGS
    local fctn=${1}
    args+=("${fctn}_")
}
```
### ex: animals

```bash
declare -A animals=()
declare -a animalNames=()

function addAnimal() {
    animalNames+=("$1")
    animals["$1_species"]="$2"
    animals["$1_age"]="$3"
    animals["$1_sound"]="$4"
}

addAnimal Milo cat 8 meow
addAnimal Rex dog 2 woof

for animal in "${animalNames[@]}"; do
    echo "${animal} is a ${animals[${animal}_species]}"
    echo "It's ${animals[${animal}_age]} years old"
    echo "It and makes ${animals[${animal}_sound]}!"
done
```

### ex: country / regions

Consider this code, that stores the areas of European regions, while also cataloging them by country:

```bash
declare -a european_regions=('Bavaria' 'Lazio' 'Saxony' 'Tuscany')
declare -a european_countries=('Italy' 'Germany')
declare -A area_of_country_regions
area_of_country_regions=( 
    ["Lazio in Italy"]="5000" 
    ["Tuscany in Italy"]="6000"
    ["Bavaria in Germany"]="9500"
    ["Saxony in Germany"]="7200" 
)
for country in "${european_countries[@]}"; do
    for region in "${european_regions[@]}"; do
        cr="${region} in ${country}"
        if test "${area_of_country_regions[$cr]+isset}"; then
            printf "Area of %-20.20s: %s" "$cr" "${area_of_country_regions[$cr]}"
        fi
    done
done
```

The code creates two normal arrays, one for countries and one for regions, plus one hash with composite keys that associate each region to its country and emulate a two-level hash. The code then generates all possible combinations of regions and countries, but only processes existing elements of area_of_country_regions, recognizing them with the *isset test of line 11. Rough, but effective, isn’t it?
## delete

```bash
declare -A numbers
numbers["four"]=4
unset numbers["four"]
```
