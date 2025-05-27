---
title: Home
layout: home
nav_order: 0
permalink: /
---

# Scripting Overview

Memento Database provides powerful scripting capabilities that allow you to extend functionality, automate tasks, and create dynamic user experiences using JavaScript. Scripts enable you to go beyond the standard database features and implement custom logic tailored to your specific needs.

## What Are Scripts?

Scripts in Memento Database are [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) code snippets that execute in response to specific events or user interactions. They provide a flexible way to:

- **Extend Functionality**: Add custom business logic that isn't available through standard database features
- **Automate Tasks**: Perform repetitive operations automatically based on triggers or user actions  
- **Enhance User Experience**: Create dynamic interfaces and intelligent data processing
- **Integrate Systems**: Connect with external APIs and services
- **Validate Data**: Implement custom validation rules and data processing workflows

## Where Scripts Are Used

- **[Triggers]({% link scripts/triggers.md %})**: Execute automatically on database events (record creation, updates, deletion, field changes)
- **[Actions and Buttons]({% scripts/actions.md %})**: Run when users click buttons or menu items in the interface
- **[Data Sources]({% scripts/data_source.md %})**: Provide dynamic data for auto-completion fields and dropdowns
- **[Widgets]({% scripts/widgets.md %})**: Program the appearance and behavior of widgets based on library data
- **[JavaScript Field]({% scripts/widgets.md %})**: Use scripts for complex calculations within entry fields  

For detailed information on accessing and manipulating library data within scripts, see the [Memento JavaScript API]({% script_api/index.md %}) documentation, which provides comprehensive reference for all available methods and objects for working with entries, fields, and library operations.

