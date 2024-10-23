<template>
  <div class="device-item">
    <div v-if="isEditing" class="editing-mode">
      <input
        v-model="localDeviceName"
        @keyup.enter="saveEdit"
        class="device-input"
      />
      <button @click="saveEdit" class="save-button">Save</button>
    </div>
    <div v-else class="view-mode">
      <span class="device-name">{{ device.name }}</span>
      <div class="button-container">
        <button @click="edit" class="edit-button">Edit</button>
        <button @click="confirmDeleteDevice" class="delete-button">
          Delete
        </button>
        <button @click="addNode" class="add-node-button">Add Node</button>
      </div>
      <ul class="node-list">
        <li v-for="(node, index) in localNodes" :key="index" class="node-item">
          <span>{{ node.name }}</span>
          <div class="button-container">
            <button @click="editNode(index)" class="edit-node-button">
              Edit
            </button>
            <button
              @click="confirmDeleteNode(index)"
              class="delete-node-button"
            >
              Delete
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  props: {
    device: Object,
  },
  setup(props, { emit }) {
    const isEditing = ref(false);
    const localDeviceName = ref(props.device.name);
    const localNodes = ref([]); // Initialize with an empty array

    onMounted(() => {
      // Load stored nodes for the specific device
      const storedNodes = JSON.parse(
        localStorage.getItem(`nodes_${props.device.id}`)
      );
      if (storedNodes) {
        localNodes.value = storedNodes; // Use the stored nodes if available
      }
    });

    const saveNodesToLocalStorage = () => {
      // Save nodes only for the current device
      localStorage.setItem(
        `nodes_${props.device.id}`,
        JSON.stringify(localNodes.value)
      );
    };

    const edit = () => {
      isEditing.value = true;
    };

    const saveEdit = () => {
      emit("edit-device", localDeviceName.value);
      isEditing.value = false;
    };

    const confirmDeleteDevice = () => {
      if (confirm("Are you sure you want to delete this device?")) {
        emit("delete-device");
        localStorage.removeItem(`nodes_${props.device.id}`); // Remove nodes for this device
      }
    };

    const addNode = () => {
      const newNodeName = prompt("Enter node name:");
      if (newNodeName) {
        const newNode = { name: newNodeName };
        localNodes.value.push(newNode);
        saveNodesToLocalStorage(); // Save the new node
      }
    };

    const editNode = (index) => {
      const newName = prompt(
        "Enter new node name:",
        localNodes.value[index].name
      );
      if (newName) {
        localNodes.value[index].name = newName; // Update the node name
        saveNodesToLocalStorage(); // Save changes
      }
    };

    const confirmDeleteNode = (index) => {
      if (confirm("Are you sure you want to delete this node?")) {
        deleteNode(index);
      }
    };

    const deleteNode = (index) => {
      localNodes.value.splice(index, 1); // Remove the node from the list
      saveNodesToLocalStorage(); // Save changes
    };

    return {
      isEditing,
      localDeviceName,
      localNodes,
      edit,
      saveEdit,
      confirmDeleteDevice,
      addNode,
      editNode,
      confirmDeleteNode,
      deleteNode,
    };
  },
};
</script>

<style lang="scss">
.device-item {
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 16px;
  margin: 10px 0;
  transition: background-color 0.3s;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);

  &:hover {
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  }

  .device-name {
    font-size: 20px;
    font-weight: bold;
    color: #333;
  }

  .editing-mode {
    .device-input {
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      width: calc(100% - 20px);
      margin-right: 10px;
    }

    .save-button {
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 5px;
      padding: 10px 15px;
      cursor: pointer;
      transition: background-color 0.3s;
      margin-top: 10px;

      &:hover {
        background-color: #0056b3;
      }
    }
  }

  .view-mode {
    .button-container {
      display: flex;
      justify-content: space-between;
      margin-top: 10px;

      .edit-button,
      .delete-button,
      .add-node-button {
        margin-left: 10px;
        border: none;
        border-radius: 5px;
        padding: 8px 12px;
        color: white;
        cursor: pointer;
        transition: background-color 0.3s;

        &.edit-button {
          background-color: #ffc107;

          &:hover {
            background-color: #e0a800;
          }
        }

        &.delete-button {
          background-color: #dc3545;

          &:hover {
            background-color: #c82333;
          }
        }

        &.add-node-button {
          background-color: #28a745;

          &:hover {
            background-color: #218838;
          }
        }
      }
    }
  }

  .node-list {
    list-style: none;
    padding: 0;

    .node-item {
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      margin-top: 25px;
      background-color: #f0f0f0;

      .button-container {
        display: flex;
        gap: 10px;

        .edit-node-button,
        .delete-node-button {
          border: none;
          border-radius: 5px;
          padding: 5px 10px;
          color: white;
          cursor: pointer;
          transition: background-color 0.3s;
          margin-top: 10px;

          &.edit-node-button {
            background-color: #ffc107;

            &:hover {
              background-color: #e0a800;
            }
          }

          &.delete-node-button {
            background-color: #dc3545;

            &:hover {
              background-color: #c82333;
            }
          }
        }
      }
    }
  }
}
</style>
