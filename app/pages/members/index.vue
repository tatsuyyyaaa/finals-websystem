<template>
  <div>
    <h1>Members Page</h1>
    <v-btn color="primary" @click="createMemberDialog = true">Add Member</v-btn>

    <v-card class="mt-4">
      <v-data-table :items="members" :headers="headers" :loading="loading">
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn
            icon="mdi-eye"
            color="blue"
            size="small"
            @click="viewMember(item)"
          ></v-btn>
          <v-btn
            icon="mdi-pencil"
            color="green"
            size="small"
            @click="editMember(item)"
          ></v-btn>
          <v-btn
            icon="mdi-delete"
            color="red"
            size="small"
            @click="deleteMember(item)"
          ></v-btn>
        </template>
      </v-data-table>
    </v-card>

    <!-- CREATE DIALOG -->
    <v-dialog width="500" v-model="createMemberDialog">
      <v-card>
        <v-toolbar color="green">
          <v-toolbar-title>Create Member</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="createForm">
            <v-text-field v-model="memberName" label="Full Name" required></v-text-field>
            <v-text-field v-model="memberEmail" label="Email" type="email" required></v-text-field>
            <v-select v-model="memberRole" :items="roles" label="Role" required></v-select>

            <v-btn @click="createMember()" color="green" block :loading="creating">Create</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- EDIT DIALOG -->
    <v-dialog width="500" v-model="editMemberDialog">
      <v-card>
        <v-toolbar color="blue">
          <v-toolbar-title>Edit Member</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="editForm">
            <v-text-field v-model="editMemberData.name" label="Full Name" required></v-text-field>
            <v-text-field v-model="editMemberData.email" label="Email" type="email" required></v-text-field>
            <v-select v-model="editMemberData.role" :items="roles" label="Role" required></v-select>

            <v-btn @click="updateMember()" color="blue" block :loading="updating">Update</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- VIEW DIALOG -->
    <v-dialog width="500" v-model="viewMemberDialog">
      <v-card>
        <v-toolbar color="info">
          <v-toolbar-title>Member Details</v-toolbar-title>
        </v-toolbar>
        <v-card-text class="pt-5">
          <v-list>
            <v-list-item>
              <v-list-item-title>Name</v-list-item-title>
              <v-list-item-subtitle>{{ viewMemberData.name }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Email</v-list-item-title>
              <v-list-item-subtitle>{{ viewMemberData.email }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Role</v-list-item-title>
              <v-list-item-subtitle>{{ viewMemberData.role }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Created At</v-list-item-title>
              <v-list-item-subtitle>{{ viewMemberData.createdAt }}</v-list-item-subtitle>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<script setup>
const members = ref([]);
const loading = ref(false);
const creating = ref(false);
const updating = ref(false);

// Dialogs
const createMemberDialog = ref(false);
const editMemberDialog = ref(false);
const viewMemberDialog = ref(false);

// Forms
const createForm = ref(null);
const editForm = ref(null);
const memberName = ref("");
const memberEmail = ref("");
const memberRole = ref("user");
const editMemberData = ref({});
const viewMemberData = ref({});

// Snackbar
const snackbar = ref(false);
const snackbarColor = ref("");
const snackbarText = ref("");

const roles = ["admin", "staff", "user"];
const headers = [
  { title: "Name", key: "name" },
  { title: "Email", key: "email" },
  { title: "Role", key: "role" },
  { title: "Date Created", key: "createdAt" },
  { title: "Actions", key: "actions", sortable: false },
];

// GET ALL MEMBERS
const getMembers = async () => {
  loading.value = true;
  try {
    const res = await $fetch("http://localhost:1337/api/members");
    if (res) {
      members.value = res.data;
    }
  } catch (err) {
    console.log("Error fetching members:", err);
    showMessage("Error loading members", "error");
  } finally {
    loading.value = false;
  }
};

// CREATE MEMBER
const createMember = async () => {
  if (!createForm.value) return;
  
  const { valid } = await createForm.value.validate();
  if (!valid) return;
  
  creating.value = true;
  try {
    let payload = {
      data: {
        name: memberName.value,
        email: memberEmail.value,
        role: memberRole.value,
      },
    };
    
    const res = await $fetch("http://localhost:1337/api/members", {
      method: "POST",
      body: payload,
    });

    showMessage("Member created successfully!", "success");
    getMembers();
    createMemberDialog.value = false;
    resetCreateForm();
    
  } catch (err) {
    console.log(err);
    showMessage("Error creating member", "error");
  } finally {
    creating.value = false;
  }
};

// VIEW MEMBER
const viewMember = (member) => {
  viewMemberData.value = { ...member };
  viewMemberDialog.value = true;
};

// EDIT MEMBER (open dialog)
const editMember = (member) => {
  editMemberData.value = { ...member };
  editMemberDialog.value = true;
};

// UPDATE MEMBER
const updateMember = async () => {
  if (!editForm.value) return;
  
  const { valid } = await editForm.value.validate();
  if (!valid) return;
  
  updating.value = true;
  try {
    let payload = {
      data: {
        name: editMemberData.value.name,
        email: editMemberData.value.email,
        role: editMemberData.value.role,
      },
    };
    
    const res = await $fetch(`http://localhost:1337/api/members/${editMemberData.value.id}`, {
      method: "PUT",
      body: payload,
    });

    showMessage("Member updated successfully!", "success");
    getMembers();
    editMemberDialog.value = false;
    
  } catch (err) {
    console.log(err);
    showMessage("Error updating member", "error");
  } finally {
    updating.value = false;
  }
};

// DELETE MEMBER
const deleteMember = async (member) => {
  if (!confirm(`Delete ${member.name}?`)) return;
  
  try {
    const res = await $fetch(
      `http://localhost:1337/api/members/${member.id}`,
      {
        method: "DELETE",
      }
    );

    showMessage("Member deleted successfully!", "success");
    getMembers();
  } catch (err) {
    console.log(err);
    showMessage("Error deleting member", "error");
  }
};

// Reset forms
const resetCreateForm = () => {
  memberName.value = "";
  memberEmail.value = "";
  memberRole.value = "user";
  if (createForm.value) {
    createForm.value.reset();
  }
};

const showMessage = (message, color = "success") => {
  snackbarText.value = message;
  snackbarColor.value = color;
  snackbar.value = true;
};

onMounted(() => {
  getMembers();
});
</script>