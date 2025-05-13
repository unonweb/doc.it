
```sh
find . -name '*.h' | zenity --list --title "Search Results" --text "Finding all header files.." --column "Files"  
zenity --list --checklist --column "Buy" --column "Item" TRUE Apples TRUE Oranges FALSE Pears FALSE Toothpaste
```