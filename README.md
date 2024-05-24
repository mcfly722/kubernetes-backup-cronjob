# kubernetes-backup-cronjob
CronJob with container for kubernetes to backup external databases to S3 or other destinations.


Project State: In Progress

Writed on Go, it has pipe flows without any temporary files to upload database backup directly to destination.<br>

Supported:
1. [ ] Compressing
2. [ ] encryption (asymmetric with public key)
3. [ ] consistency check (based on SHA1 hash)
4. [ ] check for resulting backup file size
5. [ ] rotation old backups

Source: <b>PostgreSQL</b><br>
Destination: <b>S3</b><br>

```mermaid
graph LR
    A(Start) --> B[1. dumping data<br>pg_dump]
    B --> C[2. compressing<br>bzip2]
    B --> E[3. calculating hash<br>for original content<br>sha1sum]
    C --> D[4. upload to S3<br>s3cmd]
    D --> F{5. s3cmd<br>file size<br>check}
    F --> G[6. downloading<br>backup<br>s3cmd]
    G --> H[7. uncompressing<br>bzip2]
    H --> J[8. calculating<br>saved file hash<br>sha1sum]
    J --> I{9. hash equal?}
    E --> I  
    I --> S[10. adding hash to<br>file name on S3<br>s3cmd]
    S --> R[11. rotation old backups]
    R--> Z(Finish)
```

Tests List:
1. [ ] dumping failed
1. [ ] compressing failed
3. [ ] source content hashing failed
4. [ ] uploading failed
5. [ ] resulting file size is too small (backup not created correctly)
6. [ ] downloading failed
7. [ ] uncompressing failed
8. [ ] destination content hashing failed
9. [ ] hashes of uploaded and downloaded files are not match (consistency check failed)
10. [ ] renaming failed
11. [ ] rotation of old backup faield
