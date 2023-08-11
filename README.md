# publicTEST

    <script src="investigatorRESULTS.js"></script>
    <script>
        function createTableFromJSON(data) {
            let table = document.createElement('table');
            let thead = document.createElement('thead');
            let tbody = document.createElement('tbody');

            // Assuming the structure of the first result is similar to the others
            let headers = Object.keys(data[0].result);
            let tr = document.createElement('tr');
            headers.forEach(header => {
                let th = document.createElement('th');
                th.textContent = header;
                tr.appendChild(th);
            });
            thead.appendChild(tr);
            table.appendChild(thead);

            // Creating data rows
            data.forEach(entry => {
                let row = entry.result;
                let tr = document.createElement('tr');
                headers.forEach(header => {
                    let td = document.createElement('td');
                    if (Array.isArray(row[header])) {
                        td.textContent = row[header].join(', ');
                    } else {
                        td.textContent = row[header];
                    }
                    tr.appendChild(td);
                });
                tbody.appendChild(tr);
            });
            table.appendChild(tbody);

            return table;
        }

        const tableContainer = document.getElementById('tableContainer');
        tableContainer.appendChild(createTableFromJSON(investigatorData));
    </script>
