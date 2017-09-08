# how to check value in array

In some case, we need to find if a value in an array in bash, and here is a workable function.

```bash
function array_contains
{
  arrays=(`echo ${1}`)
  target=$2

  result=false
  for item in ${arrays[@]}
  do
    if [[ "${item}X" = "${target}X" ]]
    then
      result=true
      break
    fi
  done

  echo $result
}


A=(one two three four)

echo $(array_contains "$(echo ${A[@]})" "one")
### true

echo $(array_contains "$(echo ${A[@]})" "four")
### true

echo $(array_contains "$(echo ${A[@]})" "six")
### false
```













