<template>
    <button :class="buttonClass">{{ text }}</button>
  </template>
  
  
  <script>
  export default {
    name: "actionButton",
    props: {
      text: {
        type: String,
        required: true,
        default: "Insert Text",
      },
      type: {
        type: String,
        required: false,
        default: "primary",
        validator(value) {
          return ["primary", "secondary"].includes(value);
        },
      },
    },
    data() {
      return {    
      }
  },
  methods: {
    toogleValue() {
      this.$emit("toogleValue", )
    }  
    },
    computed: {
      buttonClass() {
        return {
          [this.type]: true,
        };
      },
    },
  };
  </script>
  
  <style scoped>
  button {
    padding: 5px 10px;
    font-size: 12px;
    margin: 0 5px;
    min-width: 70px;
  }
  .primary {
    color: #313131;
    font-size: 20px;
    border-radius: 5px;
    border: none;
    box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.5);
    transition: 0.4s;
  }
  .primary:hover {
    background-color: rgba(255, 255, 255, 0.7);
    box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  }
  .secondary {
    color: white;
    font-size: 20px;
    border-radius: 5px;
    border: none;
    box-shadow: 0 0 8px rgba(207, 19, 19, 0.25);
    background-color: rgba(255, 0, 0, 0.5);
    transition: 0.4s;
  }
  .secondary:hover {
    background-color: rgba(255, 0, 0, 0.7);
    box-shadow: 0 0 16px rgba(92, 9, 9, 0.25);
  }
  </style>
  