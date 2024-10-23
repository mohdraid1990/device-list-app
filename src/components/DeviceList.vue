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
        const now = new Date();
        const formattedDate = now.toLocaleString("en-GB", {
          day: "2-digit",
          month: "2-digit",
          year: "numeric",
          hour: "2-digit",
          minute: "2-digit",
        });

        const newDevice = {
          id: Date.now(), // Unique identifier for the device
          name: deviceName,
          nodes: [],
          addedAt: formattedDate,
        };
        devices.value.push(newDevice);
        localStorage.setItem("devices", JSON.stringify(devices.value)); // Save devices
      } else {
        alert("Please enter a valid device name.");
      }
    };

    const editDevice = (index, newName) => {
      devices.value[index].name = newName;
      localStorage.setItem("devices", JSON.stringify(devices.value)); // Save devices
    };

    const deleteDevice = (index) => {
      devices.value.splice(index, 1); // Remove device
      localStorage.setItem("devices", JSON.stringify(devices.value)); // Save devices
    };

    const confirmDeleteAll = () => {
      if (confirm("Are you sure you want to delete all devices?")) {
        devices.value = [];
        localStorage.removeItem("devices"); // Clear all devices
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
  background-color: #f9f9f9; /* Changed to a lighter shade */
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15); /* Slightly darker shadow */
  text-align: center; /* Center the text */

  @media (max-width: 768px) {
    padding: 15px; /* Reduce padding for smaller screens */
  }

  h2 {
    margin-bottom: 20px;
    font-size: 2.5em; /* Slightly larger font size */
    color: #2c3e50; /* Darker color for better readability */
    text-align: center;
  }

  .button-container {
    display: flex;
    flex-direction: column;
    gap: 15px;
    align-items: center;
    justify-content: center;

    @media (min-width: 768px) {
      flex-direction: row; /* Row layout for larger screens */
    }

    .add-device-button,
    .delete-all-button {
      padding: 12px 25px; /* Increased padding */
      background-color: #3498db; /* Changed to a blue color */
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1em; /* Use em for responsive font size */
      transition: background-color 0.3s, transform 0.2s, box-shadow 0.2s; /* Added box-shadow transition */

      &:hover {
        background-color: #2980b9; /* Darker blue on hover */
        transform: translateY(-2px);
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); /* Shadow effect on hover */
      }

      &.delete-all-button {
        background-color: #e74c3c; /* Changed to a red color */

        &:hover {
          background-color: #c0392b; /* Darker red on hover */
        }
      }
    }
  }

  .device-items {
    list-style: none;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;

    .device-item {
      display: flex;
      align-items: center;
      padding: 20px;
      margin: 15px 0;
      background-color: #fff;
      border-radius: 5px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
      width: 80%;
      transition: box-shadow 0.3s;
      justify-content: center;
      flex-direction: column;

      &:hover {
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); /* Darker shadow on hover */
      }

      .device-number {
        margin-left: 0; /* Remove left margin for better alignment */
        font-weight: bold;
        font-size: 1.2em; /* Increased font size */
        color: #2c3e50; /* Darker color for better visibility */
      }

      .device-date {
        margin-left: 0; /* Remove left margin for better alignment */
        font-size: 0.9em; /* Slightly smaller font size */
        color: #7f8c8d; /* Lighter color for date */
      }

      @media (min-width: 768px) {
        .device-number {
          margin-left: 10px;
        }

        .device-date {
          margin-left: 20px;
        }
      }
    }
  }
}
</style>
