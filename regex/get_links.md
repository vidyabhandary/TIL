# Regex to retrieve links from a markdown file

Use the following regex to retrieve the links in a markdown file. (Used it for the self-updating part of TILs into the github profile readme.)

`<alt_text>` - is a string variable.

```python
search_re = re.findall( r'(\*+).(\[.*?\])(\(.*?\)).?-(.+)', <all_text>, re.M|re.I)
```


Code that retrieves the links in markdown file, sorts them and formats so it can be inserted into another markdown file.

`<markdown.md>` - is the url to the markdown file.

```python
til_readme = "<markdown.md>"
r = requests.get(til_readme)
page = requests.get(til_readme)
all_text = page.text

search_re = re.findall( r'(\*+).(\[.*?\])(\(.*?\)).?-(.+)', all_text, re.M|re.I)

dt_til = sorted(search_re, key=lambda search_re: search_re[3], reverse=True)[:5]

til_md = ""

for i in dt_til:
    til_md += "\n" + i[0] + ' ' + i[1] + i[2]         

return til_md
```

Sample input from the markdown file

**Input**

```
# til


<!-- index starts -->
## python

* [Convert a datetime object](https://github.com/vidyabhandary/til/blob/master/python/convert-to-utc.md) - 2020-07-23
* [Testing](https://github.com/vidyabhandary/til/blob/master/python/test.md) - 2020-07-22
* [Double hash works ?](https://github.com/vidyabhandary/til/blob/master/python/python_check.md) - 2020-07-25
* [Test removal / renaming of index files](https://github.com/vidyabhandary/til/blob/master/python/RIndexFiles.md) - 2020-07-20

## sqlite

* [Sql new 3 34 hash](https://github.com/vidyabhandary/til/blob/master/sqlite/sql_new_3_24.md) - 2020-07-25
* [Deleted template vars](https://github.com/vidyabhandary/til/blob/master/sqlite/deleted_template_vars.md) - 2020-07-25
<!-- index ends -->
```

**Output**

```
* [Sql new 3 34 hash](https://github.com/vidyabhandary/til/blob/master/sqlite/sql_new_3_24.md) - 2020-07-25
* [Deleted template vars](https://github.com/vidyabhandary/til/blob/master/sqlite/deleted_template_vars.md) - 2020-07-25
* [Double hash works ?](https://github.com/vidyabhandary/til/blob/master/python/python_check.md) - 2020-07-25
* [Convert a datetime object](https://github.com/vidyabhandary/til/blob/master/python/convert-to-utc.md) - 2020-07-23
* [Testing](https://github.com/vidyabhandary/til/blob/master/python/test.md) - 2020-07-22

```
