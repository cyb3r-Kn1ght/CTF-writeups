# patricks-paraflag writeups
### Team: UIT.KySiNguLam
![image](https://github.com/user-attachments/assets/05ace259-c704-4271-8be0-e25511f9a65f)

## Overview
- This is a challenge with paradoxify algorithm.
## Vulnerability
- Let take a look through main function.
```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  size_t v3; // rbx
  size_t v4; // rcx
  size_t v5; // rax
  int v6; // ebx
  char v8[256]; // [rsp+0h] [rbp-208h] BYREF
  char s[264]; // [rsp+100h] [rbp-108h] BYREF

  printf("What do you think the flag is? ");
  fflush(_bss_start);
  fgets(s, 256, stdin);
  v3 = strcspn(s, "\n");
  s[v3] = 0;
  if ( strlen(target) == v3 )
  {
    v4 = v3 >> 1;
    if ( v3 > 1 )
    {
      v5 = 0LL;
      do
      {
        v8[2 * v5] = s[v5];
        v8[2 * v5 + 1] = s[v4 + v5];
        ++v5;
      }
      while ( v5 < v4 );
    }
    v8[v3] = 0;
    printf("Paradoxified: %s\n", v8);
    v6 = strcmp(target, v8);
    if ( v6 )
    {
      puts("You got the flag wrong >:(");
      return 0;
    }
    else
    {
      puts("That's the flag! :D");
    }
  }
  else
  {
    puts("Bad length >:(");
    return 1;
  }
  return v6;
}
```
- This function compare user input with `target` global variable. Double click `target`.
![image](https://github.com/user-attachments/assets/cdf3a26b-0796-460b-ac1c-69e5394251ab)

- It seem to be a flag but it is obfuscated. `l_alcotsft{_tihne__ifnlfaign_igtoyt}`
## Exploit
- I recommend you to search info of paradox to reverse this strings.
- This is python code to reverse strings.
```python
s = "l_alcotsft{_tihne__ifnlfaign_igtoyt}"
s1 = ""
s2 = ""
for i in range(len(s)):
    if i % 2 == 0:
        s1 += s[i]
    else:
        s2 += s[i]
print("Flag: ", s1 + s2)
```
