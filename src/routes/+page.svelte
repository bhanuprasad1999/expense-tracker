<!-- Index.svelte -->
<script lang="ts">
    import Table from "../components/table/Table.svelte";

    interface Row {
        id: number;
        [key: string]: any;
    }

    interface Column {
        label: string;
        key: string;
        type:string;
    }

    let columns: Column[] = [
        { label: "Name", key: "name" , type:"text"},
        { label: "Age", key: "age", type:"number" },
        { label: "test", key: "test", type:"text" },
        { label: "test2", key: "test2", type:"text" }
    ];

    let rows: Row[] = [
        { id: 1, name: "John Doe", age: 30 },
        { id: 2, name: "Jane Doe", age: 25 },
    ];

    function handleRowAdd(newRow: Omit<Row, "id">): void {
        rows = [...rows, { id: rows.length + 1, ...newRow }];
    }

    function handleRowUpdate(updatedRow: Row): void {
        rows = rows.map((row) => (row.id === updatedRow.id ? updatedRow : row));
    }

    function handleRowDelete(rowId: number): void {
        rows = rows.filter((row) => row.id !== rowId);
    }
</script>

<Table
    {columns}
    {rows}
    onRowAdd={handleRowAdd}
    onRowUpdate={handleRowUpdate}
    onRowDelete={handleRowDelete}
/>
