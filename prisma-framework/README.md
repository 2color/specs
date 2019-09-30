# Prisma Framework

- Owner: @schickling
- Stakeholders: @sorenbs @matthewmueller @janpio
- State: 
  - Spec: In Progress 🚧
  - Implementation: In Progress 🚧

The Prisma Framework is a suite of independent tools and workflows to make working with data easier. The Prisma Framework is built for a variety of databases
and programming languages.

---

<!-- START doctoc -->
<!-- END doctoc -->

## Motivation

TODO Why the world needs Prisma Framework

## Core Objects

Core objects describe the format of the shared data structures across workflows.

### Schema

The Schema is an AST representation of Prisma Schema Language files. Workflows will work with this structured representation of the `*.prisma` files rather than
the raw file data.

### Migration

A Migration is a sequence of steps to change a datasource's structure. Some changes may include adding a new model, changing a field name or removing a
relationship. Each step is a single command we run against the datasource. A single migration runs its steps inside a transaction whenever possible.


## Core Workflows

### Introspect

Introspection is the process of understanding and reconstructing a datasource's models, fields and relationships from an existing datasource. Introspection
allows brownfield applications to get started with minimal hassle.

#### Postgres

Postgres supports a rich set of introspection capabilities. Each postgres database comes with an internal schema called the `information_schema`. This is where
you'll find metadata on the structure of our data.

#### Mongo

### Generate

Generation is the process of taking a Prisma Schema and a capability map and generating a datasource client. We call a generated datasource client **Photon**.
The API of these clients will depend on the datasource's capabilities. For example, the Postgres client will be a lot more capable than the Redis client.

### Access

Access is the how Photon builds up queries to send to the datasources. Data access has a client-server relationship.

#### Client

The Access Client is a simple HTTP client that's embedded inside Photon.

#### Server

**Editor's Note:** This is currently called the Query Engine.

The Access Server responds to HTTP requests from the Access Client. The Access Server is a long-running service that's either run alongside your application as
"sidecar process" or as a remote HTTP server. Typically when you're just getting started, you'll run the Access Server as a sidecar and later, as your business
grows, you'll transition to a multi-tiered architecture with access control.

#### Connector

The connector is a datasource-specific library that is passed into the Access Server. The Access Server is precompiled with common Connectors like Postgres,
MySQL, SQLite and MongoDB. The Access Server will delegate establishing the datasource connection to the connectors. Once the connections have been established,
the Access Server is ready for queries from the Access Client.

### Migrate

Migrate is the process of safely changing the structure of one or more datasources. Migrate has a client-server relationship.

#### Client

The Migrate Client is a runtime library that lives inside the Prisma CLI.

**TODO** finish

#### Server

The Migrate Server can run either locally as a sidecar process or remotely as a coordination server. While the Migrate Server does support HTTP for triggering
commands, it has different requirements than the Access Server. Unlike data access, migrating your data may take hours or even days to complete. We can't expect
to have a reliable connection for the duration of these long-running processes.

So additional to HTTP, the Migrate server also supports websocket for subscribing to update events.

**TODO** finish

### Manage

**TODO** studio workflows

### Schema

We provide a couple workflows for interacting with the Prisma Schema Language.

#### Parse

Parse takes a `schema.prisma` file and turns it into an AST.

#### Format

Formatter takes a `schema.prisma` as a string input and returns a string.

#### Diff

Diff finds the differences between two Prisma Schemas. We use Diff in Lift to generate a sequence of steps to migrate from one schema to the next.

**TODO** Feels like Diff shouldn't be tied to `Migration`

#### Patch

Patch applies the steps returned from a `Differ` to migrate schema one to schema two.

**TODO** Patching at this level has nothing to do with migrations, we should update the types here.

#### Assemble

Assemble takes a Schema AST and generates a string.

## Terminology

- **Schema:** The structure of your application's data. A schema contains datasources, generators, models, fields, and relationhips.
- **Prisma Schema Language:** A syntax for describing your application's Schema. You'll find the Prisma Schema Language in files with the `.prisma` extension.
- **schema.prisma:** The default file that holds your application's schema. This file is written in the Prisma Schema Language.
- **Datasource:** A resource that contains state. It could be a Postgres database or a workbook in Google Sheets.
- **Model:** A collection of related data. It could be a table in Postgres, a collection in Mongo, or a worksheet in Google Sheets.
- **Field:** A set of data of a specific type. Fields may contain strings, numbers, booleans and even binary data. These are the columns in SQL and NoSQL
  databases.
- **Record:** A single slice of data in a Model. Records are called rows in Postgres and Documents in Mongo.
- **One-to-One:** A connection between two fields of a model, where one record may be linked with only one other record.
- **One-to-Many:** A single connection between two fields of a model, where one record may be linked with many other records.
- **Many-to-Many:** A single connection between two fields of a model, where many records may be linked with many other records.
- **Connector:** A plugin that connect Prisma with the datasource's underlying stateful resource. Connectors enable the workflows described below.
- **Capability Map:** Each datasource has it's own set of unique features. The capability map is a tree of features provided by the connector to tell Prisma
  what the given datasource can do.
- **Generator:** A plugin that reads a Prisma schema and outputs code to access the datasources. Currently we have generators for Typescript and Go.
- **Brownfield Applications:** Brownfield refers to applications that already have existing infrastructure and design constraints. An example of brownfield
  application is Google Search.
- **Greenfield Applications:** Greenfield refers to applications that are starting new without constraints. An example of a greenfield application is your next
  startup.
- **AST:** AST stands for an abstract syntax tree. An abstract syntax tree is a tree structure representing the syntactic structure of a text string.
- **Prisma CLI:** A commandline interface that bundles Prisma's workflows into one tool
- **Prisma SDK:** The programmatic API to Prisma's tools.
