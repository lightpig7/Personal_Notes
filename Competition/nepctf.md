
签到

签到

与AI共舞的哈夫曼

用ai补充完整即可

#### codes

大小写绕过即可

```c
#include <stdio.h>
int main(int argc, char **argv, char **Envp) {
char **a;
for (a = Envp; *a != 0; a++) {
char *thisEnv = *a;
printf("%s\n", thisEnv);
}
}
```