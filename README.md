# cycle_logging
App to log daily exercise and and to analyse the data

One could just keep using a spreadsheet, or practise to use some other tools: mysql, python, java, ...

### Create the database
As root:
```
sudo mysql -u root
```
Run: 
```
CREATE USER fahrrad@localhost IDENTIFIED BY 'good_password';
CREATE DATABASE fahrrad;
GRANT INSERT, UPDATE, DELETE, SELECT on fahrrad.* TO fahrrad@localhost;
GRANT SELECT, LOCK TABLES ON fahrrad.* TO fahrrad@localhost;
FLUSH PRIVILEGES;
USE fahrrad;
CREATE TABLE fahrrad_rides (EntryID mediumint NOT NULL AUTO_INCREMENT PRIMARY KEY,
    Date date NOT NULL,
    DayKM float(10,3) NOT NULL,
    DaySeconds mediumint NOT NULL,
    DayKMH float(10,4),
    TotalKM float NOT NULL,
    TotalSeconds int NOT NULL,
    TotalKMH float(10,4),
    CulmKM float,
    CulmSeconds int,
    wasupdated bool DEFAULT true,
    UNIQUE (TotalKM),
    CONSTRAINT UC_daily UNIQUE (Date, DayKM, DaySeconds) );
COMMIT;
```

As user `fahrrad`:
```
mysql -u fahrrad -p
```
Run: 
```
USE fahrrad;
INSERT INTO fahrrad_rides (Date, DayKM, DaySeconds, TotalKM, TotalSeconds)
            VALUES ('2021-12-20', 19.14, 43*60+39, 90796, 3968*3600+26);
COMMIT;
SELECT * FROM fahrrad_rides;
```

More MySQL settings (e.g. fill in missing values, create summarising tables) are described in file [MySQL_scheduler.md](MySQL_scheduler.md)

### Create settings file to use for programs:
Create a file `fahrrad_mysql.params` with entries:
```
host = localhost
user = fahrrad
password = good_password
db = fahrrad
```

### Import old values from a csv file
Either tab or comma separated. The convertion between csv columns to database columns is defined in parameter **entries_for_mysql** in **import_csv_mysql.py**
```
python3 import_csv_mysql.py <csv_file.csv>
```

### Add daily values
#### Compile (Linux)
(classpath might not necessary, need if *java.lang.ClassNotFoundException: com.mysql.cj.jdbc.Drive*. The package can be downloaded from https://dev.mysql.com/downloads/connector/j/ )
```
javac -classpath /usr/share/java/mysql-connector-java-8.0.27.jar:. Fahrrad.java
```

#### Run
```
java -classpath /usr/share/java/mysql-connector-java-8.0.27.jar:. Fahrrad
```



