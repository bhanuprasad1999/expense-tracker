<script lang="ts">
    import Button from "../design/Button.svelte";

    export let columns: { label: string; key: string; type: string }[] = [];
    export let rows: { id: number; [key: string]: any }[] = [];
    export let onRowAdd: (newRow: { [key: string]: any }) => void = () => {};
    export let onRowUpdate: (updatedRow: {
        id: number;
        [key: string]: any;
    }) => void = () => {};
    export let onRowDelete: (rowId: number) => void = () => {};

    let editingRow: { id: number; [key: string]: any } | null = null;
    let newRow: { [key: string]: any } = {};

    // Initialize newRow object with empty values for each column
    $: if (columns.length > 0 && Object.keys(newRow).length === 0) {
        newRow = columns.reduce(
            (acc, column) => ({ ...acc, [column.key]: "" }),
            {},
        );
    }

    // Function to add a new row
    function addRow(): void {
        onRowAdd(newRow);
        newRow = columns.reduce(
            (acc, column) => ({ ...acc, [column.key]: "" }),
            {},
        );
    }

    // Function to start editing a row
    function startEditing(row: { id: number; [key: string]: any }): void {
        editingRow = { ...row };
    }

    // Function to update a row
    function updateRow(): void {
        if (editingRow) {
            onRowUpdate(editingRow);
            editingRow = null;
        }
    }

    // Function to cancel editing a row
    function cancelEditing(): void {
        editingRow = null;
    }

    // Function to delete a row
    function deleteRow(id: number): void {
        onRowDelete(id);
    }
</script>

<div class="table-container">
    <table
        class="custom__table"
        width="25%"
        border="1"
        cellpadding="5"
        cellspacing="0"
    >
        <thead>
            <tr>
                {#each columns as column}
                    <th>{column.label}</th>
                {/each}
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            {#each rows as row}
                <tr>
                    {#each columns as column}
                        {#if editingRow && editingRow.id === row.id}
                            <td
                                ><input
                                    type={column.type}
                                    bind:value={editingRow[column.key]}
                                /></td
                            >
                        {:else}
                            <td>{row[column.key]}</td>
                        {/if}
                    {/each}
                    <td>
                        {#if editingRow && editingRow.id === row.id}
                            <Button onClick={updateRow} variant="success"
                                >Save</Button
                            >
                            <Button onClick={cancelEditing} variant="danger"
                                >Cancel</Button
                            >
                        {:else}
                            <Button
                                onClick={() => startEditing(row)}
                                variant="primary">Edit</Button
                            >
                            <Button
                                onClick={() => deleteRow(row.id)}
                                variant="danger">Delete</Button
                            >
                        {/if}
                    </td>
                </tr>
            {/each}
            <tr>
                {#each columns as column}
                    <td
                        ><input
                            type={column.type}
                            bind:value={newRow[column.key]}
                            placeholder={column.label}
                        /></td
                    >
                {/each}
                <td>
                    <Button onClick={addRow}>Add row</Button>
                </td>
            </tr>
        </tbody>
    </table>
</div>

<style>
    .table-container {
        width: 50%;
        max-height: 100vh;
        overflow: scroll;
    }

    table {
        width: 100%;
    }

    th,
    td {
        text-align: left;
    }

    th {
        position: sticky;
        top: 0;
    }

    th:last-child,
    td:last-child {
        position: sticky;
        right: 0;
        width: auto;
        background-color: rgba(9, 8, 20, 0.671);
    }

    /* Hide scrollbar for Chrome, Safari and Opera */
    ::-webkit-scrollbar {
        display: none;
    }

    /* Hide scrollbar for IE, Edge and Firefox */
    .custom__table {
        -ms-overflow-style: none; /* IE and Edge */
        scrollbar-width: none; /* Firefox */
    }
</style>
