# Memory Inspector

This extension contributes a set of widgets for viewing memory in different ways.

## Requirements

This extension must be used in conjunction with a Debug Adapter that implements a ReadMemoryRequest handler or alternative custom request that returns memory data. It has been tested against the [CDT-GDB Adapter](https://github.com/eclipse-cdt/cdt-gdb-adapter) used as the backend for the
[CDT-GDB VSCode](https://github.com/eclipse-cdt/cdt-gdb-vscode) plugin. This repository is configured to download that plugin as part of its build routine.
If you intend to use this extension with a different debug adapter, you may need to implement a custom
[`MemoryProvider`](./src/browser/memory-provider/memory-provider-service.ts) to handle any peculiarities of the requests and responses used by your adapter.

## The Widgets

### Memory Widget

The basic [`MemoryWidget` class](./src/browser/memory-widget/memory-widget.ts) is a wrapper around two functional widgets, a `MemoryOptionsWidget` and
a`MemoryTableWidget`. The [`MemoryOptionsWidget`](./src/browser/memory-widget/memory-options-widget.tsx) is responsible for configuring the display
and fetching memory, and the [`MemoryTableWidget`](./src/browser/memory-widget/memory-table-widget.tsx) renders the memory according to the options
specified by the user in the `MemoryOptionsWidget`. The basic combination of these three classes offers variable highlighting, ascii display, and
dynamic updating in response to events from the debug session, as well as the option to lock the view to ignore changes from the session.

### Diff Widget

The [`MemoryDiffWidget`](./src/browser/diff-widget/memory-diff-widget-types.ts) is an elaboration of the `MemoryWidget` type that allows side-by-side
comparison of the contents of two `MemoryWidgets`.

### Register Widget

Although it is not supported by the `cdt-gdb-vscode` debug adapter, the Debug Adapter Protocol supports including registers as a scope inside the
response to a variables request. The [`RegisterWidget`](./src/browser/register-widget/register-widget-types.ts) offers functionality to view and
manipulate those values when using a debug adapter that reports register contents.

### Editable Widget

Another feature not presently available from `cdt-gdb-vscode` but
[proposed for the Debug Adapter Protocol](https://github.com/microsoft/debug-adapter-protocol/issues/163) and available for implementation as a custom
request to a GDB backend is direct writing of memory by address. The
[`MemoryEditableTableWidget`](./src/browser/editable-widget/memory-editable-table-widget.tsx) adds UI functionality to allow users to modify values in
the table display and send them to a backend that supports that operation.

## Using the Widgets

The widgets are created by the [`MemoryWidgetManager`](./src/browser/utils/memory-widget-manager.ts), and modifying the `createNewMemoryWidget()`
method of that service allows you to change what kind of widget is instantiated and under what circumstances. The widgets get memory through the
[`MemoryProviderService`](./src/browser/memory-provider/memory-provider-service.ts), which delegates to implementations `MemoryProvider` interface
that are bound as `MemoryProvider` contributions.
