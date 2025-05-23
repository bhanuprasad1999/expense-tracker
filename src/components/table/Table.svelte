<script lang="ts">
    import Button from "../design/Button.svelte";
    import { onMount } from 'svelte';

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
    let tableContainer: HTMLElement;
    let isMobile = false;
    let shouldFreezeActions = false;
    let isScrolling = false;

    // Initialize newRow object with empty values for each column
    $: if (columns.length > 0 && Object.keys(newRow).length === 0) {
        newRow = columns.reduce(
            (acc, column) => ({ ...acc, [column.key]: "" }),
            {},
        );
    }

    onMount(() => {
        // Detect device type
        const checkDevice = () => {
            isMobile = window.innerWidth <= 768;
            checkTableWidth();
        };

        // Check if table needs horizontal scroll
        const checkTableWidth = () => {
            if (tableContainer) {
                const table = tableContainer.querySelector('.data-table') as HTMLElement;
                if (table) {
                    shouldFreezeActions = table.scrollWidth > tableContainer.clientWidth;
                }
            }
        };

        // Handle scroll to show/hide freeze based on scroll position
        const handleScroll = () => {
            if (!tableContainer) return;
            
            isScrolling = true;
            clearTimeout(scrollTimeout);
            
            const scrollLeft = tableContainer.scrollLeft;
            const maxScroll = tableContainer.scrollWidth - tableContainer.clientWidth;
            
            // Only freeze actions column when scrolling horizontally and not at the end
            shouldFreezeActions = scrollLeft > 0 && scrollLeft < maxScroll - 10;
            
            scrollTimeout = setTimeout(() => {
                isScrolling = false;
            }, 150);
        };

        let scrollTimeout: number;

        checkDevice();
        
        window.addEventListener('resize', checkDevice);
        if (tableContainer) {
            tableContainer.addEventListener('scroll', handleScroll);
        }

        return () => {
            window.removeEventListener('resize', checkDevice);
            if (tableContainer) {
                tableContainer.removeEventListener('scroll', handleScroll);
            }
            clearTimeout(scrollTimeout);
        };
    });

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

<div class="table-wrapper">
    <div 
        class="table-container" 
        bind:this={tableContainer}
        class:mobile={isMobile}
        class:scrolling={isScrolling}
    >
        <table class="data-table">
            <thead>
                <tr>
                    {#each columns as column}
                        <th class="data-header">{column.label}</th>
                    {/each}
                    <th 
                        class="actions-header" 
                        class:frozen={shouldFreezeActions && !isMobile}
                    >
                        Actions
                    </th>
                </tr>
            </thead>
            <tbody>
                {#each rows as row}
                    <tr class="data-row">
                        {#each columns as column}
                            {#if editingRow && editingRow.id === row.id}
                                <td class="data-cell">
                                    <input
                                        class="table-input"
                                        type={column.type}
                                        bind:value={editingRow[column.key]}
                                    />
                                </td>
                            {:else}
                                <td class="data-cell" title={row[column.key]}>
                                    {row[column.key]}
                                </td>
                            {/if}
                        {/each}
                        <td 
                            class="actions-cell" 
                            class:frozen={shouldFreezeActions && !isMobile}
                        >
                            {#if editingRow && editingRow.id === row.id}
                                <div class="button-group">
                                    <Button 
                                        onClick={updateRow} 
                                        variant="success" 
                                        size="sm"
                                    >
                                        {isMobile ? '✓' : 'Save'}
                                    </Button>
                                    <Button 
                                        onClick={cancelEditing} 
                                        variant="secondary" 
                                        size="sm"
                                    >
                                        {isMobile ? '✕' : 'Cancel'}
                                    </Button>
                                </div>
                            {:else}
                                <div class="button-group">
                                    <Button
                                        onClick={() => startEditing(row)}
                                        variant="outline" 
                                        size={isMobile ? 'xs' : 'sm'}
                                    >
                                        {isMobile ? '✏️' : 'Edit'}
                                    </Button>
                                    <Button
                                        onClick={() => deleteRow(row.id)}
                                        variant="danger" 
                                        size={isMobile ? 'xs' : 'sm'}
                                    >
                                        {isMobile ? '🗑️' : 'Delete'}
                                    </Button>
                                </div>
                            {/if}
                        </td>
                    </tr>
                {/each}
                <!-- Add new row -->
                <tr class="add-row">
                    {#each columns as column}
                        <td class="data-cell">
                            <input
                                class="table-input"
                                type={column.type}
                                bind:value={newRow[column.key]}
                                placeholder={`Enter ${column.label.toLowerCase()}`}
                            />
                        </td>
                    {/each}
                    <td 
                        class="actions-cell" 
                        class:frozen={shouldFreezeActions && !isMobile}
                    >
                        <Button 
                            onClick={addRow} 
                            variant="primary" 
                            size={isMobile ? 'xs' : 'sm'}
                        >
                            {isMobile ? '+' : 'Add Row'}
                        </Button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</div>

<style>
    .table-wrapper {
        width: 100%;
        position: relative;
        border-radius: var(--radius-lg);
        overflow: hidden;
        box-shadow: var(--shadow-md);
        background-color: var(--color-bg-secondary);
        border: 1px solid var(--color-border-primary);
    }

    .table-container {
        width: 100%;
        height: 100%;
        max-height: 70vh;
        overflow: auto;
        position: relative;
        scroll-behavior: smooth;
        /* Ensure proper scrolling */
        -webkit-overflow-scrolling: touch;
    }

    /* Mobile specific container */
    .table-container.mobile {
        max-height: 50vh;
    }

    .data-table {
        width: 100%;
        min-width: 100%;
        border-collapse: separate;
        border-spacing: 0;
        font-size: var(--font-size-sm);
        table-layout: auto;
    }

    /* Header styling */
    thead {
        background-color: var(--color-bg-tertiary);
        position: sticky;
        top: 0;
        z-index: 20;
    }

    .data-header,
    .actions-header {
        padding: var(--space-md);
        text-align: left;
        font-weight: var(--font-weight-semibold);
        color: var(--color-text-primary);
        border-bottom: 2px solid var(--color-border-primary);
        white-space: nowrap;
        background-color: var(--color-bg-tertiary);
    }

    .data-header:not(:last-of-type) {
        border-right: 1px solid var(--color-border-secondary);
    }

    .actions-header {
        width: 140px;
        min-width: 140px;
        text-align: center;
        border-left: 2px solid var(--color-border-primary);
    }

    .actions-header.frozen {
        position: sticky;
        right: 0;
        z-index: 25;
        box-shadow: -2px 0 4px rgba(0, 0, 0, 0.1);
    }

    /* Row styling */
    .data-row {
        transition: background-color var(--transition-fast);
    }

    .data-row:hover {
        background-color: var(--color-surface-hover);
    }

    .add-row {
        background-color: var(--color-bg-tertiary);
        border-top: 2px solid var(--color-border-primary);
    }

    .add-row:hover {
        background-color: var(--color-surface);
    }

    /* Cell styling */
    .data-cell {
        padding: var(--space-sm) var(--space-md);
        border-bottom: 1px solid var(--color-border-secondary);
        vertical-align: middle;
        color: var(--color-text-primary);
        min-width: 120px;
        max-width: 200px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }

    .data-cell:not(:last-of-type) {
        border-right: 1px solid var(--color-border-subtle);
    }

    .actions-cell {
        width: 140px;
        min-width: 140px;
        padding: var(--space-xs) var(--space-sm);
        border-bottom: 1px solid var(--color-border-secondary);
        border-left: 2px solid var(--color-border-primary);
        text-align: center;
        vertical-align: middle;
        background-color: var(--color-bg-secondary);
    }

    .actions-cell.frozen {
        position: sticky;
        right: 0;
        z-index: 15;
        box-shadow: -2px 0 4px rgba(0, 0, 0, 0.1);
    }

    .data-row:hover .actions-cell {
        background-color: var(--color-surface-hover);
    }

    .add-row .actions-cell {
        background-color: var(--color-bg-tertiary);
    }

    .add-row:hover .actions-cell {
        background-color: var(--color-surface);
    }

    /* Button group */
    .button-group {
        display: flex;
        gap: var(--space-xs);
        justify-content: center;
        align-items: center;
        flex-wrap: nowrap;
    }

    /* Input styling */
    .table-input {
        width: 100%;
        min-width: 100px;
        font-size: var(--font-size-sm);
        padding: var(--space-xs) var(--space-sm);
        background-color: var(--color-bg-primary);
        border: 1px solid var(--color-border-primary);
        border-radius: var(--radius-sm);
        color: var(--color-text-primary);
        transition: all var(--transition-fast);
    }

    .table-input:focus {
        border-color: var(--color-accent-primary);
        box-shadow: 0 0 0 2px rgba(47, 129, 247, 0.1);
        outline: none;
    }

    .table-input::placeholder {
        color: var(--color-text-tertiary);
        font-size: var(--font-size-xs);
    }

    /* Custom scrollbar */
    .table-container::-webkit-scrollbar {
        width: 12px;
        height: 12px;
    }

    .table-container::-webkit-scrollbar-track {
        background: var(--color-bg-tertiary);
        border-radius: var(--radius-sm);
    }

    .table-container::-webkit-scrollbar-thumb {
        background: var(--color-border-muted);
        border-radius: var(--radius-sm);
        border: 2px solid var(--color-bg-tertiary);
    }

    .table-container::-webkit-scrollbar-thumb:hover {
        background: var(--color-text-tertiary);
    }

    .table-container::-webkit-scrollbar-corner {
        background: var(--color-bg-tertiary);
    }

    /* Mobile specific styles */
    @media (max-width: 768px) {
        .actions-header,
        .actions-cell {
            width: 100px;
            min-width: 100px;
        }
        
        .button-group {
            flex-direction: column;
            gap: 2px;
        }
        
        .data-header,
        .actions-header {
            padding: var(--space-xs) var(--space-sm);
            font-size: var(--font-size-xs);
        }
        
        .data-cell {
            padding: var(--space-xs) var(--space-sm);
            min-width: 80px;
            max-width: 120px;
        }

        .table-input {
            font-size: var(--font-size-xs);
            padding: 4px var(--space-xs);
        }

        /* Smaller scrollbar on mobile */
        .table-container::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
    }

    /* Firefox scrollbar */
    .table-container {
        scrollbar-width: thin;
        scrollbar-color: var(--color-border-muted) var(--color-bg-tertiary);
    }

    /* Smooth scroll indicators */
    .table-container.scrolling {
        scroll-behavior: auto;
    }

    /* Focus management for accessibility */
    .table-container:focus-within .frozen {
        outline: 1px solid var(--color-accent-primary);
        outline-offset: -1px;
    }
</style>