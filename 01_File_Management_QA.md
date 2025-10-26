# Linux File Management — Coding Questions with Answers (C)

Each entry is a concise, working C program (or small program sketch) for Linux/POSIX systems. Compile with `gcc` and run on a POSIX-compatible system.

---

## Q1. Create a new text file and write "Hello, World!" to it
```c
#include <stdio.h>
int main() {
    FILE *fp = fopen("hello.txt", "w");
    if (!fp) { perror("fopen"); return 1; }
    fprintf(fp, "Hello, World!\n");
    fclose(fp);
    return 0;
}
```

## Q2. Open an existing text file and display its contents
```c
#include <stdio.h>
int main() {
    FILE *fp = fopen("sample.txt", "r");
    if (!fp) { perror("fopen"); return 1; }
    int c;
    while ((c = fgetc(fp)) != EOF) putchar(c);
    fclose(fp);
    return 0;
}
```

## Q3. Create a new directory named "Test"
```c
#include <sys/stat.h>
#include <stdio.h>
int main() {
    if (mkdir("Test", 0755) != 0) { perror("mkdir"); return 1; }
    return 0;
}
```

## Q4. Check if "sample.txt" exists
```c
#include <unistd.h>
#include <stdio.h>
int main() {
    if (access("sample.txt", F_OK) == 0) printf("Exists\n");
    else printf("Does not exist\n");
    return 0;
}
```

## Q5. Rename "oldname.txt" to "newname.txt"
```c
#include <stdio.h>
int main() {
    if (rename("oldname.txt", "newname.txt") != 0) { perror("rename"); return 1; }
    return 0;
}
```

## Q6. Delete "delete_me.txt"
```c
#include <stdio.h>
int main() {
    if (remove("delete_me.txt") != 0) { perror("remove"); return 1; }
    return 0;
}
```

## Q7. Copy contents of one file to another
```c
#include <stdio.h>
int main() {
    FILE *src = fopen("src.txt", "r");
    FILE *dst = fopen("dst.txt", "w");
    if (!src || !dst) { perror("fopen"); return 1; }
    int c;
    while ((c = fgetc(src)) != EOF) fputc(c, dst);
    fclose(src); fclose(dst);
    return 0;
}
```

## Q8. Move a file to another directory (using rename)
```c
#include <stdio.h>
int main() {
    if (rename("file.txt", "destdir/file.txt") != 0) { perror("rename"); return 1; }
    return 0;
}
```

## Q9. List all files in the current directory
```c
#include <dirent.h>
#include <stdio.h>
int main() {
    DIR *d = opendir(".");
    struct dirent *e;
    if (!d) { perror("opendir"); return 1; }
    while ((e = readdir(d)) != NULL) printf("%s\n", e->d_name);
    closedir(d);
    return 0;
}
```

## Q10. Get the size of "file.txt"
```c
#include <sys/stat.h>
#include <stdio.h>
int main() {
    struct stat st;
    if (stat("file.txt", &st) != 0) { perror("stat"); return 1; }
    printf("Size: %lld bytes\n", (long long)st.st_size);
    return 0;
}
```

## Q11. Check if directory "Test" exists
```c
#include <sys/stat.h>
#include <stdio.h>
int main() {
    struct stat st;
    if (stat("Test", &st) == 0 && S_ISDIR(st.st_mode)) printf("Directory exists\n");
    else printf("No such directory\n");
    return 0;
}
```

## Q12. Create "Backup" in parent directory
```c
#include <sys/stat.h>
#include <stdio.h>
int main() {
    if (mkdir("../Backup", 0755) != 0) { perror("mkdir"); return 1; }
    return 0;
}
```

## Q13. Recursively list all files and directories (using nftw)
```c
#include <ftw.h>
#include <stdio.h>
static int show(const char *fpath, const struct stat *sb, int typeflag) {
    (void)sb; (void)typeflag; printf("%s\n", fpath); return 0;
}
int main(int argc, char **argv) { ftw((argc>1)?argv[1]:".", show, 20); return 0; }
```

## Q14. Delete all files in "Temp" (non-recursive)
```c
#include <dirent.h>
#include <stdio.h>
#include <string.h>
int main() {
    DIR *d = opendir("Temp"); struct dirent *e; char path[1024];
    if (!d) { perror("opendir"); return 1; }
    while ((e = readdir(d)) != NULL) {
        if (strcmp(e->d_name, ".") == 0 || strcmp(e->d_name, "..") == 0) continue;
        snprintf(path, sizeof(path), "Temp/%s", e->d_name);
        if (remove(path) != 0) perror(path);
    }
    closedir(d); return 0;
}
```

## Q15. Count number of lines in "data.txt"
```c
#include <stdio.h>
int main() {
    FILE *f = fopen("data.txt","r"); if (!f) { perror("fopen"); return 1; }
    int c, lines = 0;
    while ((c = fgetc(f)) != EOF) if (c == '\n') lines++;
    fclose(f); printf("%d\n", lines); return 0;
}
```

## Q16. Append "Goodbye!" to "message.txt"
```c
#include <stdio.h>
int main() { FILE *f = fopen("message.txt","a"); if (!f) { perror("fopen"); return 1; } fprintf(f, "Goodbye!\n"); fclose(f); return 0; }
```

## Q17. Change permissions of "file.txt" to read-only
```c
#include <sys/stat.h>
#include <stdio.h>
int main() { if (chmod("file.txt", S_IRUSR|S_IRGRP|S_IROTH) != 0) { perror("chmod"); return 1; } return 0; }
```

## Q18. Change ownership of "file.txt" to user "user1" (requires privileges)
```c
#include <pwd.h>
#include <unistd.h>
#include <stdio.h>
int main() {
    struct passwd *pw = getpwnam("user1"); if (!pw) { fprintf(stderr, "No such user\n"); return 1; }
    if (chown("file.txt", pw->pw_uid, pw->pw_gid) != 0) { perror("chown"); return 1; }
    return 0;
}
```

## Q19. Get last modified timestamp of "file.txt"
```c
#include <sys/stat.h>
#include <time.h>
#include <stdio.h>
int main() { struct stat st; if (stat("file.txt", &st) != 0) { perror("stat"); return 1; } printf("%s", ctime(&st.st_mtime)); return 0; }
```

## Q20. Create a temporary file and write data
```c
#include <stdlib.h>
#include <stdio.h>
int main() { char tmpl[] = "/tmp/mytmp.XXXXXX"; int fd = mkstemp(tmpl); if (fd==-1) { perror("mkstemp"); return 1; } FILE *f = fdopen(fd, "w"); fprintf(f, "temporary data\n"); fclose(f); printf("Created %s\n", tmpl); return 0; }
```

## Q21. Check if path is file or directory
```c
#include <sys/stat.h>
#include <stdio.h>
int main(int argc, char **argv) { if (argc<2) { fprintf(stderr,"usage\n"); return 1; } struct stat st; if (stat(argv[1], &st)!=0) { perror("stat"); return 1; } if (S_ISREG(st.st_mode)) printf("file\n"); else if (S_ISDIR(st.st_mode)) printf("directory\n"); else printf("other\n"); return 0; }
```

## Q22. Create a hard link "hardlink.txt" to "source.txt"
```c
#include <unistd.h>
#include <stdio.h>
int main() { if (link("source.txt","hardlink.txt")!=0) { perror("link"); return 1; } return 0; }
```

## Q23. Read and display CSV "data.csv"
```c
#include <stdio.h>
int main() { FILE *f = fopen("data.csv","r"); if (!f) { perror("fopen"); return 1; } char buf[4096]; while (fgets(buf,sizeof(buf),f)) fputs(buf, stdout); fclose(f); return 0; }
```

## Q24. Get absolute path of current working directory
```c
#include <unistd.h>
#include <limits.h>
#include <stdio.h>
int main() { char cwd[PATH_MAX]; if (!getcwd(cwd,sizeof(cwd))) { perror("getcwd"); return 1; } puts(cwd); return 0; }
```

## Q25. Get size of directory "Documents" (non-recursive sum)
```c
#include <dirent.h>
#include <sys/stat.h>
#include <stdio.h>
#include <string.h>
int main() { DIR *d = opendir("Documents"); struct dirent *e; struct stat st; long long total=0; char path[1024]; if (!d) { perror("opendir"); return 1; } while ((e=readdir(d))!=NULL) { if (!strcmp(e->d_name,".")||!strcmp(e->d_name,"..")) continue; snprintf(path,sizeof(path),"Documents/%s",e->d_name); if (stat(path,&st)==0 && S_ISREG(st.st_mode)) total += st.st_size; } closedir(d); printf("%lld\n", total); return 0; }
```

## Q26. Recursively copy directory (simple version)
```c
/* Simplified recursive copy: copies regular files and creates dirs. */
#include <ftw.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
static const char *src_root, *dst_root;
static void copy_file(const char *s, const char *d) {
    int inf = open(s, O_RDONLY); if (inf<0) { perror(s); return; }
    int outf = open(d, O_WRONLY|O_CREAT|O_TRUNC, 0644); if (outf<0) { perror(d); close(inf); return; }
    char buf[8192]; ssize_t r; while ((r=read(inf,buf,sizeof(buf)))>0) write(outf,buf,r);
    close(inf); close(outf);
}
static int cb(const char *fpath, const struct stat *sb, int tflag) {
    (void)sb; char rel[1024]; snprintf(rel,sizeof(rel),"%s", fpath + strlen(src_root)); char dest[2048]; snprintf(dest,sizeof(dest),"%s%s", dst_root, rel);
    if (tflag==FTW_D) mkdir(dest,0755); else if (tflag==FTW_F) copy_file(fpath,dest);
    return 0;
}
int main(int argc,char **argv) { if (argc<3) { fprintf(stderr,"usage src dst\n"); return 1; } src_root = realpath(argv[1], NULL); dst_root = argv[2]; mkdir(dst_root,0755); dst_root = realpath(dst_root, NULL); ftw(src_root, cb, 20); return 0; }
```

## Q27. Number of files in "Images"
```c
#include <dirent.h>
#include <stdio.h>
#include <string.h>
int main(){ DIR *d = opendir("Images"); if(!d){ perror("opendir"); return 1;} struct dirent *e; int c=0; while((e=readdir(d))){ if(!strcmp(e->d_name,".")||!strcmp(e->d_name,"..")) continue; c++; } closedir(d); printf("%d\n",c); return 0; }
```

## Q28. Create FIFO named "myfifo"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ if (mkfifo("myfifo", 0666) != 0) { perror("mkfifo"); return 1; } return 0; }
```

## Q29. Read data from FIFO "myfifo"
```c
#include <stdio.h>
int main(){ FILE *f = fopen("myfifo","r"); if(!f){ perror("fopen"); return 1;} char buf[1024]; while(fgets(buf,sizeof(buf),f)) fputs(buf, stdout); fclose(f); return 0; }
```

## Q30. Truncate "file.txt" to specified length (100 bytes)
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if (truncate("file.txt", 100) != 0) { perror("truncate"); return 1; } return 0; }
```

## Q31. Search for string in "data.txt" and print line numbers
```c
#include <stdio.h>
#include <string.h>
int main(){ FILE *f = fopen("data.txt","r"); if(!f){ perror("fopen"); return 1;} char line[4096]; int ln=0; while(fgets(line,sizeof(line),f)){ ln++; if(strstr(line, "search_string")) printf("%d: %s", ln, line); } fclose(f); return 0; }
```

## Q32. Get file type of a path
```c
#include <sys/stat.h>
#include <stdio.h>
int main(int argc,char **argv){ if(argc<2){ fprintf(stderr,"usage\n"); return 1;} struct stat st; if(lstat(argv[1],&st)!=0){ perror("lstat"); return 1;} if(S_ISREG(st.st_mode)) printf("regular\n"); else if(S_ISDIR(st.st_mode)) printf("directory\n"); else if(S_ISLNK(st.st_mode)) printf("symlink\n"); else printf("other\n"); return 0; }
```

## Q33. Create empty file "empty.txt"
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
int main(){ int fd = open("empty.txt", O_CREAT|O_EXCL, 0644); if(fd==-1){ perror("open"); return 1;} close(fd); return 0; }
```

## Q34. Get permissions (mode) of "file.txt"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("file.txt",&st)!=0){ perror("stat"); return 1;} printf("%o\n", st.st_mode & 0777); return 0; }
```

## Q35. Recursively delete directory "Temp" and contents
```c
#include <ftw.h>
#include <stdio.h>
#include <unistd.h>
static int rm_cb(const char *fpath,const struct stat *sb,int tflag){ (void)sb; (void)tflag; if(remove(fpath)!=0) perror(fpath); return 0; }
int main(){ ftw("Temp", rm_cb, 64); return 0; }
```

## Q36. Display first 10 lines of "log.txt"
```c
#include <stdio.h>
int main(){ FILE *f = fopen("log.txt","r"); if(!f){ perror("fopen"); return 1;} char line[4096]; int i=0; while(i<10 && fgets(line,sizeof(line),f)){ fputs(line, stdout); i++; } fclose(f); return 0; }
```

## Q37. Read "input.txt" and write to "output.txt" in reverse order (per-line)
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main(){ FILE *in=fopen("input.txt","r"); if(!in){ perror("input"); return 1;} char **lines=NULL; size_t cap=0,n=0; char buf[4096]; while(fgets(buf,sizeof(buf),in)){ if(n+1>cap){ cap=cap?cap*2:16; lines=realloc(lines,cap*sizeof(*lines)); } lines[n++]=strdup(buf); } fclose(in); FILE *out=fopen("output.txt","w"); for(ssize_t i=n-1;i>=0;--i){ fputs(lines[i], out); free(lines[i]); } free(lines); fclose(out); return 0; }
```

## Q38. Create directory named with current date YYYY-MM-DD
```c
#include <time.h>
#include <stdio.h>
#include <sys/stat.h>
int main(){ time_t t=time(NULL); struct tm *tm=localtime(&t); char name[64]; strftime(name,sizeof(name),"%Y-%m-%d",tm); if(mkdir(name,0755)!=0){ perror("mkdir"); return 1;} return 0; }
```

## Q39. Read and display contents of binary file "binary.bin"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("binary.bin","rb"); if(!f){ perror("fopen"); return 1;} unsigned char buf[512]; size_t r; while((r=fread(buf,1,sizeof(buf),f))>0) fwrite(buf,1,r,stdout); fclose(f); return 0; }
```

## Q40. Get size of largest file in a directory
```c
#include <dirent.h>
#include <sys/stat.h>
#include <stdio.h>
#include <string.h>
int main(){ DIR *d=opendir("."); struct dirent *e; struct stat st; long long max=0; char path[1024]; if(!d){ perror("opendir"); return 1;} while((e=readdir(d))){ if(!strcmp(e->d_name,".")||!strcmp(e->d_name,"..")) continue; snprintf(path,sizeof(path),"%s", e->d_name); if(stat(path,&st)==0 && S_ISREG(st.st_mode)) if(st.st_size>max) max=st.st_size; } closedir(d); printf("%lld\n", max); return 0; }
```

## Q41. Check if "data.txt" is readable
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(access("data.txt", R_OK)==0) printf("readable\n"); else printf("not readable\n"); return 0; }
```

## Q42. Create "Logs" and move all ".log" files into it
```c
#include <dirent.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
int main(){ mkdir("Logs",0755); DIR *d=opendir("."); struct dirent *e; char src[1024], dst[1024]; while((e=readdir(d))){ if(strstr(e->d_name, ".log") && strcmp(e->d_name,".") && strcmp(e->d_name,"..")){ snprintf(src,sizeof(src),"%s",e->d_name); snprintf(dst,sizeof(dst),"Logs/%s", e->d_name); if(rename(src,dst)!=0) perror("move"); } } closedir(d); return 0; }
```

## Q43. Check if "config.ini" is writable
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(access("config.ini", W_OK)==0) printf("writable\n"); else printf("not writable\n"); return 0; }
```

## Q44. Read "instructions.txt" and execute as shell commands (CAUTION)
```c
#include <stdio.h>
#include <stdlib.h>
int main(){ FILE *f=fopen("instructions.txt","r"); if(!f){ perror("fopen"); return 1;} char cmd[4096]; while(fgets(cmd,sizeof(cmd),f)){ if(cmd[strlen(cmd)-1]=='\n') cmd[strlen(cmd)-1]='\0'; system(cmd); } fclose(f); return 0; }
```

## Q45. Get number of hard links to "file.txt"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("file.txt",&st)!=0){ perror("stat"); return 1;} printf("%lu\n", (unsigned long)st.st_nlink); return 0; }
```

## Q46. Copy contents of all text files into "combined.txt"
```c
#include <dirent.h>
#include <stdio.h>
#include <string.h>
int main(){ DIR *d=opendir("."); struct dirent *e; FILE *out=fopen("combined.txt","w"); char buf[4096]; if(!out){ perror("open"); return 1;} while((e=readdir(d))){ if(strstr(e->d_name,".txt")){ FILE *in=fopen(e->d_name,"r"); if(!in) continue; while(fgets(buf,sizeof(buf),in)) fputs(buf,out); fclose(in);} } closedir(d); fclose(out); return 0; }
```

## Q47. Recursively calculate total size of files in directory
```c
#include <ftw.h>
#include <sys/stat.h>
#include <stdio.h>
static long long total=0;
static int cb(const char *f, const struct stat *sb, int t){ if(t==FTW_F) total += sb->st_size; return 0; }
int main(int argc,char **argv){ ftw((argc>1)?argv[1]:".", cb, 20); printf("%lld\n", total); return 0; }
```

## Q48. Get number of bytes in "data.bin"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("data.bin",&st)!=0){ perror("stat"); return 1;} printf("%lld\n", (long long)st.st_size); return 0; }
```

## Q49. Create directory named with current timestamp YYYY-MM-DD-HH-MM-SS
```c
#include <time.h>
#include <stdio.h>
#include <sys/stat.h>
int main(){ time_t t=time(NULL); struct tm *tm=localtime(&t); char name[64]; strftime(name,sizeof(name),"%Y-%m-%d-%H-%M-%S",tm); if(mkdir(name,0755)!=0){ perror("mkdir"); return 1;} return 0; }
```

## Q50. Create directory "Documents" in current directory
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ if(mkdir("Documents",0755)!=0){ perror("mkdir"); return 1;} return 0; }
```

## Q51. Check if "file.txt" exists
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(access("file.txt", F_OK)==0) printf("exists\n"); else printf("not exists\n"); return 0; }
```

## Q52. Open "data.txt" and display contents
```c
#include <stdio.h>
int main(){ FILE *f=fopen("data.txt","r"); if(!f){ perror("fopen"); return 1;} int c; while((c=fgetc(f))!=EOF) putchar(c); fclose(f); return 0; }
```

## Q53. Create "output.txt" and write "Hello, World!"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("output.txt","w"); if(!f){ perror("fopen"); return 1;} fprintf(f,"Hello, World!\n"); fclose(f); return 0; }
```

## Q54. Delete "delete_me.txt" from current directory
```c
#include <stdio.h>
int main(){ if(remove("delete_me.txt")!=0){ perror("remove"); return 1;} return 0; }
```

## Q55. Rename "old_name.txt" to "new_name.txt"
```c
#include <stdio.h>
int main(){ if(rename("old_name.txt","new_name.txt")!=0){ perror("rename"); return 1;} return 0; }
```

## Q56. Copy contents of one file to another
```c
#include <stdio.h>
int main(){ FILE *a=fopen("a.txt","r"), *b=fopen("b.txt","w"); if(!a||!b){ perror("fopen"); return 1;} int c; while((c=fgetc(a))!=EOF) fputc(c,b); fclose(a); fclose(b); return 0; }
```

## Q57. Move "file.txt" to directory "Backup"
```c
#include <stdio.h>
int main(){ if(rename("file.txt","Backup/file.txt")!=0){ perror("rename"); return 1;} return 0; }
```

## Q58. List all files and directories in current directory
```c
#include <dirent.h>
#include <stdio.h>
int main(){ DIR *d=opendir("."); struct dirent *e; if(!d){ perror("opendir"); return 1;} while((e=readdir(d))!=NULL) printf("%s\n", e->d_name); closedir(d); return 0; }
```

## Q59. Get size of "data.txt"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("data.txt",&st)!=0){ perror("stat"); return 1;} printf("%lld\n", (long long)st.st_size); return 0; }
```

## Q60. Create "Pictures" in parent directory
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ if(mkdir("../Pictures",0755)!=0){ perror("mkdir"); return 1;} return 0; }
```

## Q61. Count lines in "log.txt"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("log.txt","r"); if(!f){ perror("fopen"); return 1;} int c,ln=0; while((c=fgetc(f))!=EOF) if(c=='\n') ln++; fclose(f); printf("%d\n", ln); return 0; }
```

## Q62. Append "Goodbye!" to "message.txt"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("message.txt","a"); if(!f){ perror("fopen"); return 1;} fprintf(f,"Goodbye!\n"); fclose(f); return 0; }
```

## Q63. Create symbolic link "link.txt" to "target.txt"
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(symlink("target.txt","link.txt")!=0){ perror("symlink"); return 1;} return 0; }
```

## Q64. Change permissions of "file.txt" to read-only
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ if(chmod("file.txt", S_IRUSR|S_IRGRP|S_IROTH)!=0){ perror("chmod"); return 1;} return 0; }
```

## Q65. Change ownership of "file.txt" to "user1" (requires privileges)
```c
#include <pwd.h>
#include <unistd.h>
#include <stdio.h>
int main(){ struct passwd *pw=getpwnam("user1"); if(!pw){ fprintf(stderr,"No such user\n"); return 1;} if(chown("file.txt", pw->pw_uid, pw->pw_gid)!=0){ perror("chown"); return 1;} return 0; }
```

## Q66. Get last modified timestamp of "file.txt"
```c
#include <sys/stat.h>
#include <time.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("file.txt",&st)!=0){ perror("stat"); return 1;} printf("%s", ctime(&st.st_mtime)); return 0; }
```

## Q67. Create temporary file and write data
```c
#include <stdlib.h>
#include <stdio.h>
int main(){ char tmpl[]="/tmp/tmpfile.XXXXXX"; int fd=mkstemp(tmpl); if(fd==-1){ perror("mkstemp"); return 1;} FILE *f=fdopen(fd,"w"); fprintf(f,"temp data\n"); fclose(f); printf("%s\n", tmpl); return 0; }
```

## Q68. Get size of "image.jpg"
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("image.jpg",&st)!=0){ perror("stat"); return 1;} printf("%lld\n", (long long)st.st_size); return 0; }
```

## Q69. Create "notes.txt" and write multiple lines
```c
#include <stdio.h>
int main(){ FILE *f=fopen("notes.txt","w"); if(!f){ perror("fopen"); return 1;} fprintf(f,"Line 1\nLine 2\nLine 3\n"); fclose(f); return 0; }
```

## Q70. Count number of words in "essay.txt"
```c
#include <stdio.h>
#include <ctype.h>
int main(){ FILE *f=fopen("essay.txt","r"); if(!f){ perror("fopen"); return 1;} int c, inword=0; long words=0; while((c=fgetc(f))!=EOF){ if(isspace(c)){ if(inword){ words++; inword=0; } } else inword=1; } if(inword) words++; fclose(f); printf("%ld\n", words); return 0; }
```

## Q71. Create symbolic link "shortcut.txt" to "target.txt"
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(symlink("target.txt","shortcut.txt")!=0){ perror("symlink"); return 1;} return 0; }
```

## Q72. Change permissions of "important.doc" to rw------- (owner only)
```c
#include <sys/stat.h>
#include <stdio.h>
int main(){ if(chmod("important.doc", S_IRUSR|S_IWUSR)!=0){ perror("chmod"); return 1;} return 0; }
```

## Q73. Get last access time of "data.txt"
```c
#include <sys/stat.h>
#include <time.h>
#include <stdio.h>
int main(){ struct stat st; if(stat("data.txt",&st)!=0){ perror("stat"); return 1;} printf("%s", ctime(&st.st_atime)); return 0; }
```

## Q74. Read and display CSV "data.csv"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("data.csv","r"); if(!f){ perror("fopen"); return 1;} char buf[4096]; while(fgets(buf,sizeof(buf),f)) fputs(buf, stdout); fclose(f); return 0; }
```

## Q75. Truncate "file.txt" to specified length
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(truncate("file.txt", 200)!=0){ perror("truncate"); return 1;} return 0; }
```

## Q76. Search for a word in "data.txt" and show line numbers
```c
#include <stdio.h>
#include <string.h>
int main(){ FILE *f=fopen("data.txt","r"); if(!f){ perror("fopen"); return 1;} char line[4096]; int ln=0; while(fgets(line,sizeof(line),f)){ ln++; if(strstr(line,"word")) printf("%d: %s", ln, line); } fclose(f); return 0; }
```

## Q77. Get file type of a path
```c
#include <sys/stat.h>
#include <stdio.h>
int main(int argc,char **argv){ if(argc<2){ fprintf(stderr,"usage\n"); return 1;} struct stat st; if(lstat(argv[1],&st)!=0){ perror("lstat"); return 1;} if(S_ISREG(st.st_mode)) printf("regular\n"); else if(S_ISDIR(st.st_mode)) printf("directory\n"); else if(S_ISLNK(st.st_mode)) printf("symlink\n"); else printf("other\n"); return 0; }
```

## Q78. Read and display binary file "binary.bin"
```c
#include <stdio.h>
int main(){ FILE *f=fopen("binary.bin","rb"); if(!f){ perror("fopen"); return 1;} unsigned char buf[512]; size_t r; while((r=fread(buf,1,sizeof(buf),f))>0) fwrite(buf,1,r,stdout); fclose(f); return 0; }
```

## Q79. Create "Logs" and move all ".log" files into it
```c
#include <dirent.h>
#include <stdio.h>
#include <string.h>
int main(){ mkdir("Logs",0755); DIR *d=opendir("."); struct dirent *e; char src[1024], dst[1024]; while((e=readdir(d))){ if(strstr(e->d_name,".log") && strcmp(e->d_name,".") && strcmp(e->d_name,"..")){ snprintf(src,sizeof(src),"%s", e->d_name); snprintf(dst,sizeof(dst),"Logs/%s", e->d_name); if(rename(src,dst)!=0) perror("move"); } } closedir(d); return 0; }
```

## Q80. Check if "config.ini" is writable
```c
#include <unistd.h>
#include <stdio.h>
int main(){ if(access("config.ini", W_OK)==0) printf("writable\n"); else printf("not writable\n"); return 0; }
```

## Q81. Read "instructions.txt" and execute as shell commands (CAUTION)
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int main(){ FILE *f=fopen("instructions.txt","r"); if(!f){ perror("fopen"); return 1;} char cmd[4096]; while(fgets(cmd,sizeof(cmd),f)){ size_t n=strlen(cmd); if(n && cmd[n-1]=='\n') cmd[n-1]='\0'; if(strlen(cmd)) system(cmd); } fclose(f); return 0; }
```

## Q82. Read binary "data.bin" and display in hexadecimal
```c
#include <stdio.h>
int main(){ FILE *f=fopen("data.bin","rb"); if(!f){ perror("fopen"); return 1;} unsigned char buf[16]; size_t r; while((r=fread(buf,1,sizeof(buf),f))>0){ for(size_t i=0;i<r;i++) printf("%02x ", buf[i]); printf("\n"); } fclose(f); return 0; }
```

## Q83. Recursively copy all files and directories from one directory to another (simple)
```c

#include <ftw.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
static const char *src_root, *dst_root;
static void copy_file(const char *s, const char *d) {
    int inf = open(s, O_RDONLY); if (inf<0) { perror(s); return; }
    int outf = open(d, O_WRONLY|O_CREAT|O_TRUNC, 0644); if (outf<0) { perror(d); close(inf); return; }
    char buf[8192]; ssize_t r; while ((r=read(inf,buf,sizeof(buf)))>0) write(outf,buf,r);
    close(inf); close(outf);
}
static int cb(const char *fpath, const struct stat *sb, int tflag) {
    (void)sb; char rel[1024]; snprintf(rel,sizeof(rel),"%s", fpath + strlen(src_root)); char dest[2048]; snprintf(dest,sizeof(dest),"%s%s", dst_root, rel);
    if (tflag==FTW_D) mkdir(dest,0755); else if (tflag==FTW_F) copy_file(fpath,dest);
    return 0;
}
int main(int argc,char **argv) { if (argc<3) { fprintf(stderr,"usage src dst\n"); return 1; } src_root = realpath(argv[1], NULL); dst_root = argv[2]; mkdir(dst_root,0755); dst_root = realpath(dst_root, NULL); ftw(src_root, cb, 20); return 0; }
```


