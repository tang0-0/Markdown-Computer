# LinuxC文件操作接口

## 创建与删除

1. 创建文件：
    - FILE *fopen( const char *filename, const char *mode );
    - int open(const char *pathname, int flags, mode_t mode)；
2. 删除文件和空目录：int remove(const char *filename);
3. 创建目录：int mkdir(const char *pathname, mode_t mode);
4. 删除空目录：int rmdir(const char *pathname);
5. 创建软链接：int symlink(const char *oldpath, const char *newpath);
6. 创建硬链接：int link(const char *oldpath, const char *newpath);
7. 删除链接：int unlink(const char *path);

## 打开与关闭

1. 打开文件：
    - FILE *fopen( const char *filename, const char *mode );
    - int open(const char *pathname, int flags, mode_t mode)；
2. 关闭文件：
    - int fclose(FILE *stream);
    - int close(int fd);
3. 打开目录：DIR *opendir(const char *name);
4. 关闭目录：int closedir(DIR *dirp);

## 读取与写入

1. 读取数据：
    - size_t fread(void *ptr, size_t size, size_t count, FILE *stream);
    - ssize_t read(int fd, void *buf, size_t count);
2. 写入数据：
    - size_t fwrite(const void *ptr, size_t size, size_t count, FILE *stream);
    - ssize_t write(int fd, const void *buf, size_t count);
3. 偏移量
    - 设定偏移量：int fseek(FILE *stream, long offset, int origin);
    - 获取偏移量：long ftell(FILE *stream);
    - 移动偏移量到文件头：void rewind(FILE *stream)；
    - 是否到达文件尾：int feof(FILE *stream);
    - 设定偏移量：int fgetpos(FILE *stream, fpos_t *pos)；
    - 获取偏移量：int fsetpos(FILE *stream, const fpos_t *pos)
4. 读取一个字符
int fgetc(FILE *stream);
5. 写入一个字符
int fputc(int c, FILE *stream);
6. 读取一串字符串
char *fgets(char *str, int num, FILE *stream);
7. 写入一串字符串
int fputs(const char *str, FILE *stream);
8. 格式化写入字符串
int fprintf(FILE *stream, const char *format, ...);
9. 格式化读取数据
int fscanf(FILE *stream, const char *format, ...);
10. 读取目录内容：struct dirent *readdir(DIR *dirp);
11. 读取链接内容：ssize_t readlink(const char *pathname, char *buf, size_t bufsiz);

## 设置与获取参数

1. 重命名文件：int rename(const char *old_filename, const char *new_filename)；
2. 修改文件权限：int chmod(const char *path, mode_t mode);
3. 修改文件所有者和所属组：int chown(const char *path, uid_t owner, gid_t group);
4. 设置创建新文件时的权限掩码：mode_t umask(mode_t mask);
5. 修改文件访问时间和修改时间：int utime(const char *filename, const struct utimbuf *times);
6. 获取文件状态：int stat(const char *path, struct stat *buf);
7. 获取打开文件的状态：int fstat(int fd, struct stat *buf);
8. 获取符号链接文件的状态：int lstat(const char *path, struct stat *buf);

## 其它

1. 清空输入缓冲区或将输出缓冲区的数据输出到设备上：
int fflush(FILE *stream)；
2. 设置/获取文件的属性：
int fcntl(int fd, int cmd, ...);
3. 文件流转换为文件描述符：int fileno(FILE *stream); 
4. 文件描述符转换为文件流：FILE *fdopen(int fd, const char *mode);

## 监听文件操作

inotify是Linux内核提供的一种文件监控机制，能够实时监控文件系统中指定目录下的文件变化。使用inotify可以监测到文件以及目录的创建、删除、修改等操作，并及时通知相应的应用程序进行处理。

1. 创建一个新的inotify实例：int inotify_init(void);
2. 添加一个被监视的目录或文件：int  inotify_add_watch(int fd, const char *pathname, uint32_t mask);
3. 移除一个被监视的目录或文件：int inotify_rm_watch(int fd, int wd);