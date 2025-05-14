---
title: React Data Grid - Server-side data
---

# Data Grid - Server-side data 🧪

<p class="description">Learn how to work with server-side data in the Data Grid using the Data Source layer.</p>

:::warning
This feature is under development and is marked as **unstable**.
While you can use this feature in production, the API could change in the future.
Feel free to subscribe or comment on the official GitHub [umbrella issue](https://github.com/mui/mui-x/issues/8179).
:::

## Introduction

Server-side data management in React can become complex with growing datasets.
Challenges include manual data fetching, pagination, sorting, filtering, and performance optimization.
A dedicated module can help abstract these complexities to improve the developer experience.
The Data Grid provides the Data Source layer for this purpose.

### The problem: compounding complexity

Consider a Data Grid displaying a list of users that supports pagination, sorting by column headers, and filtering.
The Data Grid fetches data from the server when the user changes the page or updates filtering or sorting.
Here's an example of what that might look like:

```tsx
const [rows, setRows] = React.useState([]);
const [paginationModel, setPaginationModel] = React.useState({
  page: 0,
  pageSize: 10,
});
const [filterModel, setFilterModel] = React.useState({ items: [] });
const [sortModel, setSortModel] = React.useState([]);

React.useEffect(() => {
  const fetcher = async () => {
    // fetch data from server
    const data = await fetch('https://my-api.com/data', {
      method: 'GET',
      body: JSON.stringify({
        page: paginationModel.page,
        pageSize: paginationModel.pageSize,
        sortModel,
        filterModel,
      }),
    });
    setRows(data.rows);
  };
  fetcher();
}, [paginationModel, sortModel, filterModel]);

<DataGridPro
  columns={columns}
  pagination
  sortingMode="server"
  filterMode="server"
  paginationMode="server"
  onPaginationModelChange={setPaginationModel}
  onSortModelChange={setSortModel}
  onFilterModelChange={setFilterModel}
/>;
```

But this example only scratches the surface of the complexity when working with server-side data in the Data Grid—the following features are still not addressed:

- Performance optimization
- Data caching and request deduping
- Row grouping and tree data
- Lazy-loading data
- Handling updates to the data (row editing and deletion)
- On-demand data refetching

Trying to tackle each of these features invidually can make the code overly complex and difficult to maintain.

### The solution: the Data Source layer

For the Data Grid, the solution to the situation described above is a centralized abstraction layer called the Data Source.
This provides an interface for communications between the Data Grid on the client and the actual data on the server.

The Data Source has an initial set of required methods that you must implement.
The Data Grid uses these methods internally to fetch subsets of data as needed.

The following code snippet illustrates a minimal `GridDataSource` interface configuration.
More complex implementations with properties like `getChildrenCount()` and `getGroupKey()` are discussed in the specific server-side feature docs that follow this introductory page.

```tsx
interface GridDataSource {
  /**
   * This method is called when the grid needs to fetch rows.
   * @param {GridGetRowsParams} params The parameters required to fetch the rows.
   * @returns {Promise<GridGetRowsResponse>} A promise that resolves to the data of
   * type [GridGetRowsResponse].
   */
  getRows(params: GridGetRowsParams): Promise<GridGetRowsResponse>;
}
```

<<<<<<< HEAD
:::info

The above interface is a minimal configuration required for a data source to work.
More specific properties like `getChildrenCount` and `getGroupKey` will be discussed in the corresponding sections.

:::

# Here's how the above mentioned example would look like when implemented with the data source:

Here's what the component from [the aforementioned problem](#the-problem-compounding-complexity) looks like when implemented with the Data Source:

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
const customDataSource: GridDataSource = {
  getRows: async (params: GridGetRowsParams): GetRowsResponse => {
    const response = await fetch('https://my-api.com/data', {
      method: 'GET',
      body: JSON.stringify(params),
    });
    const data = await response.json();

    return {
      rows: data.rows,
      rowCount: data.totalCount,
    };
  },
}

<DataGridPro
  columns={columns}
  unstable_dataSource={customDataSource}
  pagination
/>
```

With the Data Source, the total amount of code is significantly reduced; there's no need to manage controlled states; and data fetching logic is centralized.

## Server-side filtering, sorting, and pagination

The Data Source changes how existing server-side features like filtering, sorting, and pagination work.

### Without the Data Source (default behavior)

<<<<<<< HEAD
When there's no data source, the features `filtering`, `sorting`, `pagination` work on `client` by default.
In order for them to work with server-side data, you need to set them to `server` explicitly and provide the [`onFilterModelChange`](https://mui.com/x/react-data-grid/filtering/server-side/), [`onSortModelChange`](https://mui.com/x/react-data-grid/sorting/#server-side-sorting), [`onPaginationModelChange`](https://mui.com/x/react-data-grid/pagination/#server-side-pagination) event handlers to fetch the data from the server based on the updated variables.
=======
Without the Data Source implemented, features like filtering, sorting, pagination are designed to work on the client side by default.

To work correctly with server-side data, you must explicitly set these features to `"server"` mode and provide the [`onFilterModelChange()`](/x/react-data-grid/filtering/server-side/), [`onSortModelChange()`](/x/react-data-grid/sorting/#server-side-sorting), and [`onPaginationModelChange()`](/x/react-data-grid/pagination/#server-side-pagination) event handlers to fetch the data from the server based on the updated variables, as shown below:

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
<DataGrid
  columns={columns}
  rows={rows}
  pagination
  sortingMode="server"
  filterMode="server"
  paginationMode="server"
  onPaginationModelChange={(newPaginationModel) => {
    // fetch data from server
  }}
  onSortModelChange={(newSortModel) => {
    // fetch data from server
  }}
  onFilterModelChange={(newFilterModel) => {
    // fetch data from server
  }}
/>
```

### With the Data Source

<<<<<<< HEAD
When the corresponding models update, the Data Grid calls the `getRows` method with the updated values of type `GridGetRowsParams` to get updated data.
=======
With the Data Source implemented, features like filtering, sorting, and pagination are automatically set to `"server"` mode.
When the corresponding models update, the Data Grid calls the `getRows()` method with the updated values of type `GridGetRowsParams` to get updated data.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
<DataGridPro
  columns={columns}
  // automatically sets `sortingMode="server"`, `filterMode="server"`, `paginationMode="server"`
  unstable_dataSource={customDataSource}
/>
```

The following demo showcases this behavior.

{{"demo": "ServerSideDataGrid.js", "bg": "inline"}}

:::info
<<<<<<< HEAD
The data source demos use a utility function `useMockServer` to simulate the server-side data fetching.
In a real-world scenario, you should replace this with your own server-side data-fetching logic.
=======
The Data Source demos use a `useMockServer()` utility function to simulate server-side data fetching.
In a real-world scenario you would replace this with your own server-side data-fetching logic.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

Open info section of the browser console to see the requests being made and the data being fetched in response.
:::

## Data caching

<<<<<<< HEAD
The data source caches fetched data by default.
This means that if the user navigates to a page or expands a node that has already been fetched, the grid will not call the `getRows` function again to avoid unnecessary calls to the server.
=======
The Data Source caches fetched data by default.
This means that if the user navigates to a page or expands a node that has already been fetched, the Grid will not call the `getRows()` function again to avoid unnecessary calls to the server.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

By default, the Grid uses `GridDataSourceCacheDefault`, which is a simple in-memory cache that stores the data in a plain object.
You can see it working in the [Data Source demo above](#with-the-data-source).

<<<<<<< HEAD

### Customize the cache lifetime

# The `GridDataSourceCacheDefault` has a default Time To Live (`ttl`) of 5 minutes. To customize it, pass the `ttl` option in milliseconds to the `GridDataSourceCacheDefault` constructor, and then pass it as the `unstable_dataSourceCache` prop.

### Improve the cache hit rate

To increase the cache hit rate, the Data Grid splits `getRows()` results into chunks before storing them in the cache.
For the requests that follow, chunks are combined as needed to recreate the response.
This means that a single request can make multiple calls to the `get()` or `set()` method of `GridDataSourceCache`.

Chunk size is the lowest expected amount of records per request based on the `pageSize` value from the `paginationModel` and `pageSizeOptions` props.
As a result, the values in the `pageSizeOptions` prop play an important role in the cache hit rate.
We recommend using values that are multiples of the lowest value—even better if each subsequent value is a multiple of the previous value.

Here are some examples:

- **Best-case scenario** – `pageSizeOptions={[5, 10, 50, 100]}`

  In this case the chunk size is 5, which means that with `pageSize={100}` there are 20 cache records stored.
  Retrieving data for any other `pageSize` up to the first 100 records results in a cache hit, since the whole dataset can be made of the existing chunks.

- **Parts of the data missing** – `pageSizeOptions={[10, 20, 50]}`

  Loading the first page with `pageSize={50}` results in 5 cache records.
  This works well with `pageSize={10}`, but not as well with `pageSize={20}`.
  Loading the third page with `pageSize={20}` results in a new request being made, even though half of the data is already in the cache.

- **Incompatible page sizes** – `pageSizeOptions={[7, 15, 40]}`

  In this situation, the chunk size is 7.
  Retrieving the first page with `pageSize={15}` creates chunks split into `[7, 7, 1]` records.
  Loading the second page creates three new chunks (again `[7, 7, 1]`), but now the third chunk from the first request has an overlap of 1 record with the first chunk of the second request.
  These chunks with 1 record can only be used as the last piece of a request for `pageSize={15}` and are useless in all other cases.

In the examples above, `sortModel` and `filterModel` remain unchanged.
Changing these would require a new response to be retrieved and stored in the chunks.

### Customize the cache lifetime

The `GridDataSourceCacheDefault` has a default time to live (TTL) of 5 minutes.
To customize this, pass the `ttl` option with a numerical value (in milliseconds) to the `GridDataSourceCacheDefault` constructor, then pass that to the `dataSourceCache` prop.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
import { GridDataSourceCacheDefault } from '@mui/x-data-grid-pro';

const lowTTLCache = new GridDataSourceCacheDefault({ ttl: 1000 * 10 }); // 10 seconds

<DataGridPro
  columns={columns}
  unstable_dataSource={customDataSource}
  unstable_dataSourceCache={lowTTLCache}
/>;
```

{{"demo": "ServerSideDataGridTTL.js", "bg": "inline"}}

### Create a custom cache

<<<<<<< HEAD
To provide a custom cache, use `unstable_dataSourceCache` prop, which could be either written from scratch or based on another cache library.
=======
Use the `dataSourceCache` prop to provide a custom cache, which may be written from scratch or based on a third-party cache library.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))
> > > > > > > This prop accepts a generic interface of type `GridDataSourceCache`.

```tsx
export interface GridDataSourceCache {
  set: (key: GridGetRowsParams, value: GridGetRowsResponse) => void;
  get: (key: GridGetRowsParams) => GridGetRowsResponse | undefined;
  clear: () => void;
}
```

### Disable caching

<<<<<<< HEAD
To disable the data source cache, pass `null` to the `unstable_dataSourceCache` prop.
=======
To disable the Data Source cache, pass `null` to the `dataSourceCache` prop.

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
<DataGridPro
  columns={columns}
  unstable_dataSource={customDataSource}
  unstable_dataSourceCache={null}
/>
```

{{"demo": "ServerSideDataGridNoCache.js", "bg": "inline"}}

<<<<<<< HEAD

## Error handling

You could handle the errors with the data source by providing an error handler function using the `unstable_onDataSourceError`.
It will be called whenever there's an error in fetching the data.

# The first argument of this function is the error object, and the second argument is the fetch parameters of type `GridGetRowsParams`.

## Updating server-side data

The Data Source supports an optional `updateRow()` method for updating data on the server.

This method returns a promise that resolves when the row is updated.
If the promise resolves, the Grid updates the row and mutates the cache.
If there's an error, `onDataSourceError()` is triggered with the error object containing the params described in the [Error handling section](#error-handling) that follows.

```diff
 const dataSource: GridDataSource = {
  getRows: async (params: GridGetRowsParams) => {
    // fetch rows from the server
  },
+ updateRow: async (params: GridUpdateRowParams) => {
+   // update row on the server
+ },
 }
```

{{"demo": "ServerSideEditing.js", "bg": "inline"}}

:::warning
When using the `updateRow()` method, the Data Source cache is automatically cleared after successful updates to prevent displaying outdated data.
This means any previously cached data will be refetched on the next request.

For applications that require caching with editing operations, consider implementing server-side caching instead.

If you have a specific use case that requires preserving the client-side cache during edit operations, please [open an issue on GitHub](https://github.com/mui/mui-x/issues/new/choose) to help us understand your requirements.
:::

:::warning
The position and visibility of the edited row on the current page are maintained—even if features like sorting or filtering are enabled—and will take affect on the row after the values update.
Any changes to the position or visibility will be applied when the page is fetched again.

You can manually trigger a refetch by calling the `dataSource.fetchRows()` API method.
:::

## Error handling

You can handle errors with the Data Source by providing an error handler function with `onDataSourceError()`.
This gets called whenever there's an error in fetching or updating the data.
This function recieves an error object of type `GridGetRowsError | GridUpdateRowError`.
Each error type has a corresponding `error.params` type which is passed as an argument to the callback:

| **Error type**       | `error.params` **type** |
| :------------------- | :---------------------- |
| `GridGetRowsError`   | `GridGetRowsParams`     |
| `GridUpdateRowError` | `GridUpdateRowParams`   |

> > > > > > > e66e24465 ([docs][data grid] Revise server-side data docs (#17007))

```tsx
<DataGridPro
  columns={columns}
  unstable_dataSource={customDataSource}
  unstable_onDataSourceError={(error, params) => {
    console.error(error);
  }}
/>
```

The demo below renders a custom Snackbar component to display an error message when the requests fail, which you can simulate using the checkbox and the **Refetch rows** button at the top.
Caching has been disabled for simplicity.

{{"demo": "ServerSideErrorHandling.js", "bg": "inline"}}

## Updating data 🚧

This feature is yet to be implemented, when completed, the method `unstable_dataSource.updateRow` will be called with the `GridRowModel` whenever the user edits a row.
It will work in a similar way as the `processRowUpdate` prop.

Feel free to upvote the related GitHub [issue](https://github.com/mui/mui-x/issues/13261) to see this feature land faster.

## API

- [DataGrid](/x/api/data-grid/data-grid/)
- [DataGridPro](/x/api/data-grid/data-grid-pro/)
- [DataGridPremium](/x/api/data-grid/data-grid-premium/)
