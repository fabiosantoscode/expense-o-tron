<script>
  import JSZip from 'jszip'

  export let expenses

  let hasExpenses = false
  $: hasExpenses = expenses.length > 0

  let downloadBlob = null

  const buildExcel = async () => {
    var wb = XLSX.utils.book_new()
    wb.Props = {
      Title: 'Expenses',
      Subject: 'Expenses',
      Author: 'Expense-O-Tron',
      CreatedDate: new Date(),
    }
    wb.SheetNames.push('Expenses')

    var ws_data = [
      ['Receipt file name', 'Description', 'Date', 'Time', 'Cost'],
      ...expenses.map(({ file, description, date, time, cost }) => [
        file?.name ?? '',
        description,
        date,
        time,
        cost,
      ]),
    ]
    wb.Sheets['Expenses'] = XLSX.utils.aoa_to_sheet(ws_data)

    var wbout = XLSX.write(wb, { bookType: 'xlsx', type: 'array' })
    return wbout
  }

  const buildZip = async () => {
    const zip = new JSZip()

    zip.file('expenses.xlsx', await buildExcel())

    for (const { file } of expenses) {
      if (file) zip.file(file.name, file)
    }

    return URL.createObjectURL(
      await zip.generateAsync({
        type: 'blob',
        compression: 'DEFLATE',
      })
    )
  }

  const onDownload = async (event) => {
    try {
      downloadBlob = await buildZip()
    } catch (e) {
      debugger
      alert(e.message)
    }
  }
</script>

{#if hasExpenses}
  <div>
    <button on:click={onDownload}>Prepare expense report</button>
    {#if downloadBlob}
      <a href={downloadBlob} class="download" download>Download!</a>
    {/if}
  </div>
{/if}

<style>
  .download {
    display: block;
    padding: 1.5em;
    text-align: center;
    font-size: 1.33em;
    color: #ff3e00;
    font-weight: bold;
  }
</style>
