# Data integration service

**Fliplet Agent (Data integration service)** is a command line utility to synchronize data to and from Fliplet Servers.

---

## Before you start

To synchronize your data with Fliplet servers you will need an authorisation token generated via Fliplet Studio from an organisation admin account.

To generate a token, please follow the docs [here](REST-API/authenticate.md).

---

## Install

Our software runs on **Node.js**, a popular open-source and cross-platform software.

Please install [node.js](http://nodejs.org/) (which also comes bundled with the popular package manager [npm](http://npmjs.com)) on your computer as a pre-requisite to get started.

Once you have installed Node.js, please open the shell to continue and install our agent:

- **Windows**: access the Node.js shell from **Start menu** > **Node.js command prompt**
- **Mac OSX**: access the shell of your computer from **Applications** > **Utilities** > **Terminal**
- **Unix**: access the shell of your computer

Then, run this simple command to install the Fliplet Agent on your machine via the npm package manager:

```bash
npm install fliplet-agent -g
```

You can now use the command `fliplet-agent` from the command line. Just type `fliplet-agent` to ensure the software is installed and also see the available options and their example usage.

---

## Update the agent to the latest version

If you need to update the agent to the latest version available on npm, run the following command from the Node.js shell:

```bash
npm update -g
```

## Get started

Create a simple file with with `.yml` extension (or grab a [sample copy here](https://raw.githubusercontent.com/Fliplet/fliplet-agent/master/sample.yml)) somewhere in your filesystem with the following configuration details and replace with your own settings where appropriate:

```yml
# Fliplet API authorisation token taken from Fliplet Studio. More documentation available at:
# https://developers.fliplet.com/REST-API/authenticate.html#how-to-create-an-authentication-token
auth_token: eu--123456789

# Define the ID of the target Fliplet Data Source where data will be pushed to.
# This ID can be retrieved from the "Manage app data" section of Fliplet Studio
# under the settings tab
datasource_id: 123

# Database connection settings below

# Possible values for the driver are: mysql, sqlite, postgres, mssql
database_driver: mssql
database_host: localhost
database_username: sampleuser
database_password: 123456
database_port: 5432
database_name: eu

# MSSQL Server only: uncomment if you need to use these variables.
# database_domain: sampleDomainName
# database_instance: sampleInstanceName
# database_encrypt: true

# ODBC Native driver only: uncomment this and install the driver on your computer
# by pasting this command to the terminal: "npm install sequelize-odbc-mssql -g"
# database_native_odbc: true

# Description of the operation (will be printed out in the logs).
description: Push my users to Fliplet every 15 minutes

# If set to true, the sync will also run when the script starts.
# Otherwise, it will only run according to the frequency setting above.
sync_on_init: true

# Frequency of running using unix cronjob syntax.
# Syntax is [Minute] [Hour] [Day of month] [Month of year] [Day of week]
# You can find many examples here https://crontab.guru/examples.html
# When testing, if you have init sync enabled your agent will sync as soon as it is run
# so restarting the agent is the fastest way to test if the configuration is working.
# A few examples here below. Feel free to uncomment the line you need:
# frequency: '0 */2 * * *'  # every 2 hours
# frequency: '0 8 * * *'    # every day at 8am
# frequency: '0 0 * * 0'    # every week
frequency: '*/15 * * * *' # every 15 minutes

# The query to run to fetch the data to be pushed to Fliplet.
# The column names must match the data source or new columns will be added,
# use SQL "AS" to map database columns to Fliplet data source column names
# and avoid new columns from being created.
query: SELECT id, email as 'Email', fullName as 'Full Name', updatedAt FROM users;

# Define which column should be used as primary key
# to understand whether a record already exists on the Fliplet Data Source.
# If you don't define this, every time the script runs rows will be appended
# to the Fliplet Data Source without running a comparison on whether the row
# has already been added.
primary_column: id

# Define which (optional) column should be used to compare whether
# the record has been updated on your database since it got inserted
# to the Fliplet Data Source hence might require updating
timestamp_column: updatedAt

# Define which (optional) column should be used to compare whether
# the record has been flagged as deleted on your database and should
# be removed from the Fliplet Data Source when the column value is not null.
delete_column: deletedAt
```

Once you have a configuration file like the one above saved on disk, starting the agent is as simple as running the `start` command from your shell. While you are setting up the configuration we also suggest using the `--test` option to perform a dry run and test out the integration without actually sending data to Fliplet servers:

```bash
fliplet-agent start ./path/to/configurationFile.yml --test
```

e.g. if your file is in the current folder and it's named `sample.yml`, you would simply write:

```bash
fliplet-agent start sample.yml
```

**Note: on Windows we do recommend using an absolute path to the config file to avoid errors when the file is loaded by the software.**

Once you start the agent with the above command an output similar to the one below will be produced:

![sample](https://user-images.githubusercontent.com/574210/45174672-c12aeb80-b20b-11e8-806e-bda5f0e521b0.png)

Any error found in your configuration will be printed out for you to look at.

---

## Install the agent as a service (Windows only)

On Windows you can install any number instances of the agent to run as a service on your machine. Behind the scene we use [node-windows](https://github.com/coreybutler/node-windows) to make this happen, however this comes bundled with the Fliplet Agent already hence installing the latter as a service is just about running this command:

```
fliplet-agent install C:\path\to\sample.yml
```

Once you run the above command, you're likely to get asked for confirmation by the OS. A series of 3-4 popups similar to these ones will appear:

![img](assets/img/agent-service.png)

Just click "**Yes**" to grant access to the software to install the service on your machine. You can monitor the output of the script through Windows Event Viewer checking under the "Applications" logs:

![img](assets/img/agent-event-viewer.png)

Likewise, you can manage the service by accessing the Windows "Services" application. To remove the service, simply run the `uninstall` command as below:

```
fliplet-agent uninstall C:\path\to\sample.yml
```

---

## Advanced use (requires a JavaScript config file)

**Note: this documentation only applies to users using a JavaScript configuration file instead of the simpler YML (YAML) approach.**

Running the Fliplet Agent in advanced mode requires you to create a configuration file written in JavaScript (instead of YML) with the following required details:

1. **Fliplet authToken**: The authorisation token generated via Fliplet Studio.
2. **Database connection details**: Username, password, host, port and database name to connect to your database server.
3. A list of **operations** to run: each operation defines how data is pushed, pulled or synced between your database and Fliplet servers.

Here's a sample configuration file to give you an idea on its structure:

```js
// Save this into a file and run using "fliplet-agent start ./path/to/file.js"

module.exports.config = {
  // Fliplet authorisation token from Fliplet Studio
  authToken: 'eu--123456789',

  // Set to true to test the integration without sending any data to Fliplet servers
  isDryRun: false,

  // If set to true, operations will run when the script starts.
  // Otherwise, they will just run according to their frequency.
  syncOnInit: true,

  // Database connection settings (using Sequelize format)
  // http://docs.sequelizejs.com/
  database: {
    dialect: 'mssql',
    host: 'localhost',
    username: 'foo',
    password: 'bar',
    port: 1234,
    database: 'myDatabaseName',

    // MSSQL Server only
    dialectOptions: {
      domain: 'myDomain',
      instanceName: 'myInstanceName',
      encrypt: false
    }
  }
};

module.exports.setup = (agent) => {

  // Push data from your table to a Fliplet Data Source
  agent.push({
    // Description of your operation (will be printed out in the logs)
    description: 'Pushes data from my table to Fliplet',

    // Frequency of running using unix cronjob syntax
    frequency: '* * * * *',

    // The query (or operation) to run to fetch the data to be pushed to Fliplet.
    // You should define a function returning a promise with the data.
    // In our example, we fetch the data using a SQL query from the local database.
    sourceQuery: (db) => db.query('SELECT id, email, "updatedAt" FROM users order by id asc;'),

    // Define which column should be used as primary key
    // to understand whether a record already exists on the Fliplet Data Source
    primaryColumnName: 'id',

    // Define which (optional) column should be used to compare whether
    // the record has been updated on your database since it got inserted
    // to the Fliplet Data Source hence might require updating
    timestampColumnName: 'updatedAt',

    // Define which (optional) column should be used to compare whether
    // the record has been flagged as deleted on your database and should
    // be removed from the Fliplet Data Source
    deleteColumnName: deletedAt,

    // The ID of the Fliplet Data Source where data should be inserted to
    targetDataSourceId: 123
  });

  // You can define any other operation similar to the above here using "agent.push()"

};
```

You can define as many push operations as you want inside a single configuration file. **They will be run in series and scheduled for future running according to the frequency set**.

Once you have a configuration file like the one above saved on disk, starting the agent is as simple as running the following command from your shell:

```bash
fliplet-agent start ./path/to/configurationFile.js
```

---

In the future, support for pulling data and a two-ways data sync will be made available.