**Table** 
```
BEGIN
   EXECUTE IMMEDIATE 'DROP TABLE mytable';
EXCEPTION
   WHEN OTHERS THEN
      IF SQLCODE != -942 THEN
         RAISE;
      END IF;
END;
```

**Sequence**
```
BEGIN
  EXECUTE IMMEDIATE 'DROP SEQUENCE ' || sequence_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -2289 THEN
      RAISE;
    END IF;
END;
```

**View**
```
BEGIN
  EXECUTE IMMEDIATE 'DROP VIEW ' || view_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -942 THEN
      RAISE;
    END IF;
END;
```

**Trigger**
```
BEGIN
  EXECUTE IMMEDIATE 'DROP TRIGGER ' || trigger_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -4080 THEN
      RAISE;
    END IF;
END;
```

**Index**
```
BEGIN
  EXECUTE IMMEDIATE 'DROP INDEX ' || index_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -1418 THEN
      RAISE;
    END IF;
END;
```

**Column**

```
BEGIN
  EXECUTE IMMEDIATE 'ALTER TABLE ' || table_name
                || ' DROP COLUMN ' || column_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -904 THEN
      RAISE;
    END IF;
END;
```

**Database** Link
```
BEGIN
  EXECUTE IMMEDIATE 'DROP DATABASE LINK ' || dblink_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -2024 THEN
      RAISE;
    END IF;
END;
```

**Materialized** View
```
BEGIN
  EXECUTE IMMEDIATE 'DROP MATERIALIZED VIEW ' || mview_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -12003 THEN
      RAISE;
    END IF;
END;
```

**Type**
```
BEGIN
  EXECUTE IMMEDIATE 'DROP TYPE ' || type_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -4043 THEN
      RAISE;
    END IF;
END;
```

**Constraint**
```
BEGIN
  EXECUTE IMMEDIATE 'ALTER TABLE ' || table_name
            || ' DROP CONSTRAINT ' || constraint_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -2443 THEN
      RAISE;
    END IF;
END;
```

**Scheduler** Job
```
BEGIN
  DBMS_SCHEDULER.drop_job(job_name);
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -27475 THEN
      RAISE;
    END IF;
END;
```

**User / Schema**

```
BEGIN
  EXECUTE IMMEDIATE 'DROP USER ' || user_name;
  /* you may or may not want to add CASCADE */
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -1918 THEN
      RAISE;
    END IF;
END;
```

**Package**

```
BEGIN
  EXECUTE IMMEDIATE 'DROP PACKAGE ' || package_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -4043 THEN
      RAISE;
    END IF;
END;
```

**Procedure**

```
BEGIN
  EXECUTE IMMEDIATE 'DROP PROCEDURE ' || procedure_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -4043 THEN
      RAISE;
    END IF;
END;
```

**Function**

```
BEGIN
  EXECUTE IMMEDIATE 'DROP FUNCTION ' || function_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -4043 THEN
      RAISE;
    END IF;
END;
```

**Tablespace**

```
BEGIN
  EXECUTE IMMEDIATE 'DROP TABLESPACE' || tablespace_name;
EXCEPTION
  WHEN OTHERS THEN
    IF SQLCODE != -959 THEN
      RAISE;
    END IF;
END;
```

