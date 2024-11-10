There are six user-defined types:

- Distinct type
- Structured type
- Reference type
- Array type
- Row type
- Cursor type
  
  
## Distinct type

A distinct type is a user-defined data type that shares its internal representation with an existing built-in data type (its "source" type).

Distinct types include qualified identifiers. If the schema name is not used to qualify the distinct type name when used in other than the CREATE TYPE (Distinct), DROP, or COMMENT statements, the SQL path is searched in sequence for the first schema with a distinct type that matches.

Distinct types that are sourced on LOB types are subject to the same restrictions as their source type.

A distinct type is defined to use either strong typing or weak typing rules. Strong typing rules are the default.



	Strongly typed distinct type
	
	A strongly typed distinct type is considered to be a separate and incompatible type for most operations. For example, you want to define a picture type, a text type, and an audio type. Each of these types has different semantics, but each uses the built-in data type BLOB for their internal representation.
	
	The following example illustrates the creation of a distinct type named AUDIO:

```
   CREATE TYPE AUDIO  AS BLOB (1M)
```

Although AUDIO has the same representation as the built-in data type BLOB, it is considered to be a separate type; this consideration allows the creation of functions that are written specifically for AUDIO, and assures that these functions are not applied to values of any other data type (for example pictures or text).

Strongly typed distinct types support strong typing by ensuring that only those functions and operators that are explicitly defined on the distinct type can be applied to its instances. For this reason, a strongly typed distinct type does not automatically acquire the functions and operators of its source type, because these functions and operators might not be meaningful. For example, a LENGTH function could be defined to support a parameter with the data type AUDIO that returns length of the object in seconds instead of bytes.


Weakly typed distinct type

A weakly typed distinct type is considered to be the same as its source type for all operations, except when the weakly typed distinct type applies constraints on values during assignments or casts. This consideration also applies to function resolution.

The following example illustrates the creation of a distinct type named POSITIVEINTEGER:

```
   CREATE TYPE POSITIVEINTEGER AS INTEGER
         WITH WEAK TYPE RULES CHECK (VALUE>=0)
```

Weak typing means that except for accepting only positive integer values, POSITIVEINTEGER operates in the same way as its underlying data type of INTEGER.

A weakly typed distinct type can be used as an alternative method of referring to a built-in data type within application code. The ability to define constraints on the values that are associated with the distinct type provides a method for checking values during assignments and casts.

Using distinct types can provide benefits in the following categories:

Extensibility

By defining new data types, you increase the set of data types available to you to support your applications.

Flexibility

You can specify any semantics and behavior for your new data type by using user-defined functions (UDFs) to augment the diversity of the data types available in the system.

Consistent and inherited behavior

Strong typing guarantees that only functions defined on your distinct type can be applied to instances of the distinct type. Weak typing ensures that the distinct type behaves the same way as its underlying data type and so can use all the same functions and methods available to that underlying type.

Encapsulation

Using a weakly typed distinct type makes it possible to define data type constraints in one location for all usages within application code for that distinct type.

Performance

Distinct types are highly integrated into the database manager. Because distinct types are internally represented the same way as built-in data types, they share the same efficient code that is used to implement components such as built-in functions, comparison operators, and indexes for built-in data types.

Not all built-in data types can be used to define distinct types. The source data type cannot be XML, array, row, or cursor. .

## Structured type
A structured type is a user-defined data type that has a structure that is defined in the database. It contains a sequence of named _attributes_, each of which has a data type. A structured type also includes a set of method specifications.

A structured type can be used as the type of a table, view, or column. When used as a type for a table or view, that table or view is known as a _typed table_ or _typed view_. For typed tables and typed views, the names and data types of the attributes of the structured type become the names and data types of the columns of this typed table or typed view. Rows of the typed table or typed view can be thought of as a representation of instances of the structured type. When used as a data type for a column, the column contains values of that structured type (or values of any of the subtypes for that type, as defined later in this section). Methods are used to retrieve or manipulate attributes of a structured column object.

A _supertype_ is a structured type for which other structured types, called _subtypes_, are defined. A subtype inherits all the attributes and methods of its supertype and can have additional attributes and methods defined. The set of structured types that is related to a common supertype is called a type _hierarchy_ and the type that does not have any supertype is called the _root type_ of the type hierarchy.

The term subtype applies to a user-defined structured type and all user-defined structured types that are below it in the type hierarchy. Therefore, a subtype of a structured type T is T and all structured types below T in the hierarchy. A _proper subtype_ of a structured type T is a structured type below T in the type hierarchy.

There are restrictions on having recursive type definitions in a type hierarchy. For this reason, it is necessary to develop a shorthand way of referring to the specific type of recursive definitions that are allowed. The following definitions are used:

- _Directly uses_: A type **A** is said to directly use another type **B**, if and only if one of the following statements is true:
    1. Type **A** has an attribute of type **B**.
    2. Type **B** is a subtype of **A**or a supertype of **A**.
- _Indirectly uses_: A type **A** is said to indirectly use a type **B**, if one of the following statements is true:
    1. Type **A** directly uses type **B**.
    2. Type **A** directly uses some type **C**and type **C** indirectly uses type **B**.

A type cannot be defined so that one of its attribute types directly or indirectly uses itself. If it is necessary to have such a configuration, consider using a reference as the attribute. For example, with structured type attributes, there cannot be an instance of "employee" with an attribute of "manager" when "manager" is of type "employee". There can, however, be an attribute of "manager" with a type of REF(employee).

A type cannot be dropped if certain other objects use the type, either directly or indirectly. For example, a type cannot be dropped if a table or view column makes direct or indirect use of the type.

## Reference type

A _reference type_ is a companion type to a structured type. Similar to a distinct type, a reference type is a scalar type that shares a common representation with one of the built-in data types. This same representation is shared for all types in the type hierarchy. The reference type representation is defined when the root type of a type hierarchy is created. When using a reference type, a structured type is specified as a parameter of the type. This parameter is called the _target type_ of the reference.

The target of a reference is always a row in a typed table or a typed view. When a reference type is used, it can have a _scope_ defined. The scope identifies a table (called the _target table_) or view (called the _target view_) that contains the target row of a reference value. The target table or view must have the same type as the target type of the reference type. An instance of a scoped reference type uniquely identifies a row in a typed table or typed view, called the _target row_.

## Array type

A user-defined _array type_ is a data type that is defined as an array with elements of another data type. Every ordinary array type has an index with the data type of INTEGER and has a defined maximum cardinality. Every associative array has an index with the data type of INTEGER or VARCHAR and does not have a defined maximum cardinality.

## Row type

A _row type_ is a data type that is defined as an ordered sequence of named fields, each with an associated data type, which effectively represents a row. A row type can be used as the data type for variables and parameters in SQL PL to provide simple manipulation of a row of data.

## Cursor type
A user-defined _cursor type_ is a user-defined data type defined with the keyword CURSOR and optionally with an associated row type. A user-defined cursor type with an associated row type is a _strongly typed cursor type_; otherwise, it is a _weakly typed cursor type_. A value of a user-defined cursor type represents a reference to an underlying cursor.