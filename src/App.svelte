<script lang="ts">
  import employees from './lib/employees.json'
  import edit from './assets/edit.svg'
  import close from './assets/x.svg'

  let employeesData = employees;
  let open = false;

  type Current = {
    photo: string,
    employeeKey: string,
    firstName: string,
    lastName: string,
    department: string,
    active: boolean,
    created: string,
    comment?: string
  }

  let current: Current = {
    photo: "",
    employeeKey: "",
    firstName: "",
    lastName: "",
    department: "",
    active: false,
    created: "",
    comment: ""
  };

  function handleSelectRow(_: MouseEvent, employee: Current) {
    document.documentElement.style.overflow = "hidden";

    // make a copy of the employee data for the modal
    current = {...employee};
    open = true;
  }

  // handle closing the modal without saving
  function handleCloseModal() {
    // find original employee and reset the current one to it
    const original = employeesData.find((employee) => employee.employeeKey === current.employeeKey);
    if (original) {
      current = original;
    }
    document.documentElement.style.overflow = "auto";
    open = false;
  }

  // close modal on escape key press
  document.addEventListener('keydown', (event) => {
    if (event.key === 'Escape') {
      handleCloseModal();
    }
  });

  function handleSubmitForm(event: Event) {
    event.preventDefault();
    // replace original with modified employee
    employeesData = employeesData.map((employee) => {
      if (employee.employeeKey === current.employeeKey) {
        return current;
      }
      return employee;
    });
    open = false;
  }

</script>

<main>
  <table id="TableContainer">
    <thead>
      <tr>
        <th>Personalnummer</th>
        <th>Foto</th>
        <th>Name</th>
        <th>Abteilung</th>
        <th>Status</th>
        <th>Erstellt</th>
        <th>Kommentar</th>
      </tr>
    </thead>
    <tbody>
      {#each employeesData as employee}
        <tr class="employee-row" on:click={(event) => handleSelectRow(event, employee)}>
          <td class="key">{employee.employeeKey.toUpperCase()}</td>
          <td><img class="avatar-img" src={`/src/assets/${employee.photo}`} alt={`${employee.firstName} ${employee.lastName} photo`} width="100" height="100" /></td>
          <td>{employee.firstName} {employee.lastName} <span class="edit"><img class="edit-icon" src={edit} alt="edit icon"/></span></td>
          <td class="tag"><span class="{employee.department.toLowerCase()}">{employee.department}</span></td>
          <td class="tag"><span class={employee.active ? "aktiv" : "inaktiv"}>{employee.active ? "Aktiv" : "Inaktiv"}</span></td>
          <td>{new Date(employee.created).toLocaleDateString("de")}</td>
          {#if employee.comment}
            <td class="comment">{employee.comment}</td>
          {:else}
            <td></td>
          {/if}
        </tr>
      {/each}
    </tbody>
    <tfoot>
      <tr>
        <td>{employeesData.length} Einträge</td>
      </tr>
    </tfoot>
  </table>
  <div id="ModalContainer" class={open ? "visible" : "hide"}>
      <button id="BgModal" class={open ? "visible" : "hide"} on:click={handleCloseModal}></button>
      <div id="Modal" class={open ? "go-up" : ""}>
        <div class="modal-close-heading"><button on:click={handleCloseModal} class="modal-close"><img src={close} alt="close icon"/></button></div>
        <div class="modal-content flex">
            <div class="modal-form flex">
              <div class="modal-title dark">Aktuelle Daten</div>
              <form on:submit={(event) => handleSubmitForm(event)}>
                <label>
                  Personalnummer
                  <input bind:value={current.employeeKey} name="personalnummer" type="text"/>
                </label>
                <label>
                  Vorname
                  <input bind:value={current.firstName} name="vorname" type="text"/>
                </label>
                <label>
                  Nachname
                  <input bind:value={current.lastName} name="nachname" type="text"/>
                </label>
                <label>
                  Abteilung
                  <select bind:value={current.department} name="abteilung">
                    <option value="Other">Other</option>
                    <option value="HR">HR</option>
                    <option value="Management">Management</option>
                    <option value="IT">IT</option>
                </label>
                <label class="checkbox">
                  <input type="checkbox" name="status" value="aktiv" bind:checked={current.active}/> {current.active ? "Aktiv" : "Inaktiv"}
                </label>
                <div class="div-submit-btn flex">
                  <button type="submit">Speichern</button>
                </div>
              </form>
            </div>
            <div class="modal-preview flex">
              <div class="modal-title dark">Preview</div>
              <div class="modal-card flex">
                <div><img src={`/src/assets/${current.photo}`} alt={current.photo} class="modal-img" /></div>
                <div class="modal-card-content flex">
                  <div>
                    <div class="modal-title gray">{current.firstName + " " + current.lastName}</div>
                    <div class="gray bold">{current.employeeKey.toUpperCase()}</div>
                  </div>
                  <div class="tag">
                    <span class={current.department.toLowerCase()}>{current.department}</span>
                    <span class={current.active ? "aktiv" : "inaktiv"}>{current.active ? "Aktiv" : "Inaktiv"}</span>
                  </div>
                </div>
              </div>
            </div>
        </div>
    </div>
  </div>  
</main>
