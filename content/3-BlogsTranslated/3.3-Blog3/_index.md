---
title: "Blog 3"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Extend the Amazon Q Developer CLI with Model Context Protocol (MCP) for Richer Context

by Brian Beach | on 29 APR 2025 | in **Amazon Aurora**, **Amazon Q Developer**, **Announcements**, **PostgreSQL compatible**, **RDS for PostgreSQL**

Earlier today, **Amazon Q Developer** announced **Model Context Protocol (MCP)** support in the command line interface (CLI). Developers can connect external data sources to Amazon Q Developer CLI with MCP support for more context-aware responses. By integrating MCP tools and prompts into Q Developer CLI, you get access to an expansive list of pre-built integrations or any MCP Servers that support stdio. This extra context helps Q Developer write more accurate code, understand your data structures, generate appropriate unit tests, create database documentation, and execute precise queries, all without needing to develop custom integration code. By extending Q Developer with MCP tools and prompts, developers can execute development tasks faster, streamlining the developer experience. At AWS, we’re committed to supporting popular open source protocols for agents like Model Context Protocol (MCP) proposed by Anthropic. We’ll continue to support this effort by extending this functionality within the Amazon Q Developer IDE plugins in the coming weeks.

## Introduction

I’m always on the lookout for tools and technologies that can streamline my workflow and unlock new capabilities. That’s why I was excited about the recent addition of Model Context Protocol (MCP) support in the Amazon Q Developer command line interface (CLI). MCP is an open protocol that standardizes how applications can seamlessly integrate with LLMs, providing a common way to share context, access data sources, and enable powerful AI-driven functionality. You can read more about MCP in [this introduction](https://modelcontextprotocol.io/docs/getting-started/intro).

Q Developer has had the ability to use tools for a while. I previously discussed the ability to run CLI commands and describe AWS resources. With the Q Developer CLI’s support for MCP tools and prompts, I now have the ability to add additional tools. For example, while I have had the ability to describe my AWS resources, I also need to describe database schemas, message formats, etc. to build an application. Let’s see how I can configure MCP to provide this additional context.

In this post, I will configure an MCP server to provide Q Developer with my database schema for a simple Learning Management System (LMS) that I am working on. While Q Developer is great at writing SQL, it does not know the schema of my database. The table structure and relationships are stored in the database and are not part of the source code of my project. Therefore, I am going to use an MCP server that can query the database schema. Specifically, I am using the official PostgreSQL reference implementation to connect to my Amazon Relational Database Service (RDS). Let’s get started.

## Before Model Context Protocol

Prior to the introduction of MCP support, the Q Developer CLI provided a set of native tools, including the ability to execute bash commands, interact with files and the file system, and even make calls to AWS services. However, when it came to querying a database, the CLI was limited in its capabilities.

For example, prior to configuring the MCP server, I asked Q Developer to “Write a query that lists the students and the number of credits each student is taking.” In the following image you can see that Q Developer could only provide a generic SQL query, as it lacked the specific knowledge of the database schema for my LMS.

![Blog-3](/images/3-Blog/Blog-3/mcp001.png)

While this is a great start, I know that Q developer could do so much more if it knew the database schema.

## Configuring Model Context Protocol

The introduction of MCP support in the Q Developer CLI allows me to easily configure MCP servers. I configure one or more MCP servers in a file called `mcp.json`. I can store the configuration in my home directory (e.g. `~/.aws/amazonq/mcp.json`) and it is applied to all projects on my machine. Alternatively, I can store the configuration in the workspace root (e.g. `.amazonq/mcp.json`) so it is shared among project members. Here is an example of the configuration for the PostgreSQL MCP server.

```bash
JSON
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://USERNAME:PASSWORD@HOST:5432/DBNAME"
      ]
    }
  }
}
```

With the MCP server configured, let’s see how Amazon Q Developer enhances my experience.

## After Model Context Protocol

First, I start a new Q Developer session and immediately see the benefits. In addition to the existing tools, Q Developer now has access to PostgreSQL as shown in the following image. This means I can easily explore the schema of my database, understand the structure of the tables, and even execute complex SQL queries, all without having to write any additional integration code.

![Blog-3](/images/3-Blog/Blog-3/mcp002.png)

Let’s test the MCP server by asking Q Developer to “List the database tables.” As you can see in the following example, Q Developer now understands that I am asking about the PostgreSQL database, and uses the MCP server to list my three tables: students, courses, and enrollment.

![Blog-3](/images/3-Blog/Blog-3/mcp003.png)

Let’s go back to the example from earlier in this post. Now, when I ask Q Developer to “Write a query that lists the students and the number of credits each student is taking,” it no longer responds with a generic query. Instead, Q Developer first describes the relevant tables in my database, generates the appropriate SQL query, and then executes it, providing me with the desired results.

![Blog-3](/images/3-Blog/Blog-3/mcp004.png)

Of course, Q Developer can do a lot more than just write queries. Q Developer can use the MCP server to write Java code that accesses the database, create unit tests for the data layer, document the database, and much more. For example, I asked Q Developer to “Create an entity-relationship (ER) diagram using Mermaid syntax.” Q Developer was able to generate a visual representation of the database schema, helping me better understand the relationships between the various entities.

![Blog-3](/images/3-Blog/Blog-3/mcp005.png)

The integration of MCP into the Q Developer CLI has significantly streamlined my workflow by allowing me to add additional tools as needed.

## Conclusion

The addition of MCP support in the Amazon Q Developer CLI provides a standardized way to share context and access data sources. In this post, I’ve demonstrated how I can use the Q Developer CLI’s MCP integration to quickly set up a connection to a PostgreSQL database, explore the schema, and generate complex SQL queries without having to write any additional integration code. Moving forward, I’m excited to see how you can leverage MCP to further enhance your development workflow. I encourage you to [explore the MCP capabilities](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html) and the [AWS MCP Servers](https://github.com/awslabs/mcp) repository on GitHub.

TAGS: [Developer Tools](https://aws.amazon.com/blogs/devops/tag/developer-tools/), [Development](https://aws.amazon.com/blogs/devops/tag/development/)
