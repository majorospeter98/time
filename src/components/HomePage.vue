<template>
  <div class="container mt-4">
      <button @click="showForm = true" class="btn btn-primary mb-3">Új bejegyzés</button>
   <div class="">
        <button @click="viewMode = 'daily' " class="btn btn-primary ml-18">Napi nézet</button>
          <button @click="viewMode = 'weekly' " class="btn btn-secondary ml-18">Heti nézet</button>
            <button @click="viewMode = 'monthly' " class="btn btn-primary ml-18">Havi nézet</button>
</div>
          
    <div v-if="showForm || showEditForm " class="modal show" style="display: block;">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
          <h5 class="modal-title">{{ this.showEditForm ? 'Szerkesztés' : 'Új bejegyzés' }}</h5>
            <button type="button" class="close" @click="closeForms">
              <span>&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="onSave">
              <div class="form-group">
                <label for="date">Dátum:</label>
                <input type="date" :min="today" v-model="newEntry.date" class="form-control" required />
              </div>
              <div class="form-group">
                <label for="startTime">Kezdési idő:</label>
                <input type="time" v-model="newEntry.startTime" class="form-control" required />
              </div>
              <div class="form-group">
                <label for="endTime">Befejezési idő:</label>
                <input type="time" v-model="newEntry.endTime" class="form-control" required />
              </div>
              <div class="form-group">
                <label for="description">Leírás:</label>
                <textarea v-model="newEntry.description" class="form-control" required></textarea>
              </div>
              <div class="form-group">
    <label for="tag">Címke:</label>
    <select v-model="newEntry.tag" class="form-control">
      <option disabled value="">Válassz egy címkét</option>
      <option v-for="tag in tags" :key="tag" :value="tag">{{ tag }}</option>
    </select>
  </div>
              <button type="submit" class="btn btn-success">Mentés</button>
              <button type="button" class="btn btn-secondary" @click="closeForms">Mégse</button>
            </form>
          </div>
        </div>
      </div>
    </div>

    <div v-if="entries && entries.length > 0">
      <ul class="list-group">
      <li v-for="(entry, index) in entries" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
  <div>
    {{ entry.id }} - {{ entry.date }} - {{ entry.startTime }} - {{ entry.endTime }} - {{ entry.description }} <span :class="{
  'project': entry.tag === 'Projekt',
  'client': entry.tag === 'Ügyfél',
  'admin': entry.tag === 'Admin'
}">
  {{ entry.tag }}
</span>
  </div>
  <div>
    <button class="btn btn-warning btn-sm mr-2" @click="editEntrie(entry)">Edit</button>
    <button class="btn btn-danger btn-sm" @click="deleteEntrie(entry.id)">Törlés</button>
  </div>
</li>
      </ul>
    </div>
    <!-- Ha nincs bejegyzés -->
    <div v-else>
      <p>Nincsenek bejegyzések.</p>
    </div>
      </div>
</template>
<script>

export default {
  data() {
    return {
      showForm: false,
      showEditForm: false,
       viewMode: 'daily', 
      entries: [],
      newEntry: {
        id: Date.now(),
        date: "",
        startTime: "",
        endTime: "",
        description: "",
        tag: ""
      },
      tags: ["Projekt", "Ügyfél", "Admin"]
    };
  },
  created() {
    this.loadEntries(); // app inicializiálás után  betölti a localstorage elementeket
  },
  methods: {
       loadEntries() { // load localstorage elements
      const savedEntries = JSON.parse(localStorage.getItem("workEntries") || "[]");
      this.entries = savedEntries;
    },
 closeForms() {
  this.showEditForm = false;
  this.showForm = false;
},
    onSave() {  //Mentés gombra nyomva hozzadás/szerkesztés
      if (this.newEntry.startTime >= this.newEntry.endTime) {  // Ellenrőzés
        alert("A kezdési idő nem lehet nagyobb vagy egyenlő a befejezési idővel.");
        return; // Kilépünk a függvényből, ha az ellenőrzés nem sikerült
      }
      // Új bejegyzés hozzáadása az entries tömbhöz
      if(!this.showEditForm){
  this.entries.push({ ...this.newEntry });
      }
          // Bejegyzések mentése localStorage-ba
      localStorage.setItem("workEntries", JSON.stringify(this.entries));

      // reset
      this.newEntry = { id: Date.now(), date: "", startTime: "", endTime: "", description: "" };
       alert("Sikeres hozzáadás!")
      this.showForm = false; 
      this.showEditForm= false
    },
    editEntrie(entry){
      this.showEditForm=true
        //reset
      this.newEntry = entry;
           
       // Űrlap bezárása
    },
     // Törlés
    deleteEntrie(id) { 
      let findSelectedItem = JSON.parse(localStorage.getItem("workEntries"));    //összes store lékrezdeése
      let newEntries = findSelectedItem.filter(item => item.id !== id); // Kiválasztott item
      this.entries = newEntries;
      localStorage.setItem("workEntries", JSON.stringify(this.entries));
      alert("Sikeres törlés!")
    }
  },
  computed: {
    today() {
    return new Date().toISOString().split("T")[0]; //annak ellenrőzése hogy a mai napnál ne lehessen régebbit bejelöllni.
  },
  filteredEntries() {
    if (this.viewMode === 'monthly') {
      // Havi nézet itt az összes entrien kkelene valahgy filterezni dátumokkal.
      return ""
    } else if (this.viewMode === 'weekly') {
      // Heti nézet
           return ""
    
    } else {
      // Napi nézet
      return this.entries;
    }
  },
}
};
</script>
<style>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1050; 
}
.project {
  background-color: #4CAF50; 
  color: white;
  padding: 2px 6px;
  border-radius: 4px;
}
.client {
  background-color: #2196F3; 
  color: white;
  padding: 2px 6px;
  border-radius: 4px;
}
.admin {
  background-color: #f44336; 
  color: white;
  padding: 2px 6px;
  border-radius: 4px;
}
</style>