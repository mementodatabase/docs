---
title: Home
layout: home
nav_order: 0
permalink: /
---

# Scripting Overview

Memento Database provides powerful scripting capabilities that allow you to extend functionality, automate tasks, and create dynamic user experiences using [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript). Scripts enable you to go beyond the standard database features and implement custom logic tailored to your specific needs.

## Where Scripts Are Used

- **[Triggers]({% link scripts/triggers.md %})**: Execute automatically on database events (record creation, updates, deletion, field changes)
- **[Actions and Buttons]({% link scripts/actions.md %})**: Run when users click buttons or menu items in the interface
- **[Data Sources]({% link scripts/data_source.md %})**: Provide dynamic data for auto-completion fields and dropdowns
- **[Widgets]({% link scripts/widgets.md %})**: Program the appearance and behavior of widgets based on library data
- **[JavaScript Field]({% link scripts/field.md %})**: Use scripts for complex calculations within entry fields  

For detailed information on accessing and manipulating library data within scripts, see the **[Memento JavaScript API]({% link script_api/index.md %})** documentation, which provides comprehensive reference for all available methods and objects for working with entries, fields, and library operations.

{: .note } 
Scripts in JavaScript Field run in a limited execution context and do not support methods from the Memento JavaScript API.

