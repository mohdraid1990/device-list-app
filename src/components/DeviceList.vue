<template>
  <div class="device-list">
    <h2>Device List</h2>
    <div class="button-container">
      <button class="add-device-button" @click="addDevice">Add Device</button>
      <button
        v-if="devices.length > 1"
        class="delete-all-button"
        @click="confirmDeleteAll"
      >
        Delete All
      </button>
    </div>
    <ul class="device-items">
      <li v-for="(device, index) in devices" :key="index" class="device-item">
        <DeviceItem
          :device="device"
          @edit-device="editDevice(index, $event)"
          @delete-device="deleteDevice(index)"
        />
        <span class="device-number">{{ index + 1 }}</span>
        <span class="device-date">Added on: {{ device.addedAt }}</span>
        <!-- Display date -->
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import DeviceItem from "./DeviceItem.vue";

export default {
  components: { DeviceItem },
  setup() {
    const devices = ref([]);

    // Load devices from localStorage when the component is mounted
    onMounted(() => {
      const storedDevices = localStorage.getItem("devices");
      if (storedDevices) {
        devices.value = JSON.parse(storedDevices);
      }
    });

    const addDevice = () => {
      const deviceName = prompt("Enter device name:");
      if (deviceName && deviceName.trim() !== "") {
        // Get the current date and time
        const now = new Date();
        const formattedDate = now.toLocaleString("en-GB", {
          day: "2-digit",
          month: "2-digit",
          year: "numeric",
          hour: "2-digit",
          minute: "2-digit",
        });

        const newDevice = {
          name: deviceName,
          nodes: [],
          addedAt: formattedDate,
        };
        devices.value.push(newDevice);
        localStorage.setItem("devices", JSON.stringify(devices.value));
      } else {
        alert("Please enter a valid device name.");
      }
    };

    const editDevice = (index, newName) => {
      devices.value[index].name = newName;
      localStorage.setItem("devices", JSON.stringify(devices.value));
    };

    const deleteDevice = (index) => {
      devices.value.splice(index, 1);
      localStorage.setItem("devices", JSON.stringify(devices.value));
    };

    const confirmDeleteAll = () => {
      if (confirm("Are you sure you want to delete all devices?")) {
        devices.value = [];
        localStorage.removeItem("devices");
      }
    };

    return { devices, addDevice, editDevice, deleteDevice, confirmDeleteAll };
  },
};
</script>

<style lang="scss">
.device-list {
  max-width: 800px;
  margin: 0 auto; /* Center the container */
  padding: 20px;
  background-color: #f7f9fc; /* Light background for contrast */
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  text-align: center; /* Center the text */

  h2 {
    margin-bottom: 20px;
    font-size: 28px; /* Slightly larger font size */
    color: #2f5552;
    text-align: center;
  }

  .button-container {
    display: flex;
    justify-content: center;
    gap: 10px; /* Space between buttons */

    .add-device-button,
    .delete-all-button {
      padding: 12px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
      transition: background-color 0.3s, transform 0.2s;

      &:hover {
        background-color: #218838;
        transform: translateY(-2px);
      }

      &.delete-all-button {
        background-color: #dc3545;

        &:hover {
          background-color: #c82333;
        }
      }
    }
  }

  .device-items {
    list-style: none;
    padding: 0;

    .device-item {
      display: flex;
      justify-content: space-between;
      align-items: center; /* Align items vertically */
      padding: 15px;
      margin: 10px 0;
      background-color: #fff; /* White background for devices */
      border-radius: 5px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);

      &:hover {
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15); /* Darker shadow on hover */
      }

      .device-number {
        margin-left: 10px;
        font-weight: bold;
        color: #555;
      }

      .device-date {
        margin-left: 20px;
        font-size: 14px;
        color: #888;
      }
    }
  }
}
</style>
