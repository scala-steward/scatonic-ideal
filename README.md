# Overview

Provides the ability to create an Ideal DB schema representation in memory 
and then compare the prototype schema to an existing schema (using JDBC Database Metadata) to calculate with differences.

This project also provides Case Class wrappers to the JDBC rows returned in DatabaseMetadata ResultSets.

# Prototyping 

Create an "ideal" Schema using the [Ideal case classes](core/src/main/scala/com/bones/si/ideal/package.scala).

```$scala
  case class IdealColumn(name: String, dataType: DataType.Value, nullable: Boolean, remark: Option[String])
  case class IdealPrimaryKey(name: String, column: ProtoColumn)
  case class IdealForeignKey(name: String, column: ProtoColumn, foreignReference: (ProtoTable, ProtoColumn))
  case class IdealTable(name: String, columns: List[ProtoColumn], foreignKeys: List[ProtoForeignKey], remark: Option[String])
  case class IdealSchema(name: String, tables: List[ProtoTable])
```

Here is an [Example](jdbc/src/it/scala/com/bones/si/jdbc/ExampleTest.scala) which creates a schema from an "ideal" structure.
  1. Create the "ideal" in memory using the case classes
  2. Create a [diff](core/src/main/scala/com/bones/si/ideal/Diff.scala) by comparing the DB Metadata to the "ideal"
  3. Create SQL statements from the diff using this project.  (eg. "create table ...")  
  4. Execute the statements to sync the DB with the "ideal"
  5. Make changes to the "ideal" (such as add a new table and change a column data type)
  6. Create a second diff by Compare the updated DB metadata to the new "ideal"
  7. Create SQL statements from the second diff using this libary. (eg "alter table ....")
  8. Execute the statements to sync the DB with the "idea"




# Database Cache
This project also provides [Case Class Wrappers for JDBC Database Metadata](https://github.com/scala-bones/db-prototyping/blob/master/core/src/main/scala/com/bones/mdwrap/package.scala)
which may be useful outside the context of creating "ideal" schema.

```$scala
    val con: Connection = ???
    val cacheQuery = DatabaseQuery.everything
    val cache = LoadDatabaseCache.load(cacheQuery, List.empty, con) 
```

Supports:
  Column, CrossReference, PrimaryKey, Table, Schema, ImportedKeys, Function, TablePrivilege, TypeInfo, Procedure
  
TODO:
  Attribute (untested because not supported in PostgreSQL), 
  
  
# Dependency

Scatonic Ideal is cross compiled for Scala 2.12 and 2.13.

```libraryDependencies += "io.github.scala-bones" %% "jdbc-scatonic-ideal" % "0.3.0"```

You will need to add one of the supported DB JDBC Drivers, which for now, is Postgres.

# Database Support

  This project has been tested and used only with PostgreSQL.

### CI
[![CircleCI](https://circleci.com/gh/scala-bones/scatonic-ideal.svg?style=svg)](https://circleci.com/gh/scala-bones/scatonic-ideal)


### Automatic Library Updates
[![Scala Steward badge](https://img.shields.io/badge/Scala_Steward-helping-blue.svg?style=flat&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAQCAMAAAARSr4IAAAAVFBMVEUAAACHjojlOy5NWlrKzcYRKjGFjIbp293YycuLa3pYY2LSqql4f3pCUFTgSjNodYRmcXUsPD/NTTbjRS+2jomhgnzNc223cGvZS0HaSD0XLjbaSjElhIr+AAAAAXRSTlMAQObYZgAAAHlJREFUCNdNyosOwyAIhWHAQS1Vt7a77/3fcxxdmv0xwmckutAR1nkm4ggbyEcg/wWmlGLDAA3oL50xi6fk5ffZ3E2E3QfZDCcCN2YtbEWZt+Drc6u6rlqv7Uk0LdKqqr5rk2UCRXOk0vmQKGfc94nOJyQjouF9H/wCc9gECEYfONoAAAAASUVORK5CYII=)](https://scala-steward.org)

 
