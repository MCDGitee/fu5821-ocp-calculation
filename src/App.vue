<script setup>
import { FormKit, FormKitSchema } from "@formkit/vue";
import { ref, onMounted } from 'vue'
import { getNode } from '@formkit/core'


const sensor_resistor_list = ref([{ 'value': 0.01, 'label': '10mOhm' }, { 'value': 0.02, 'label': '20mOhm' }, { 'value': 0.05, 'label': '50mOhm' }]);
const amplifier_ratio_list = ref([{ 'value': 1, 'label': 'x1' }, { 'value': 2, 'label': 'x2' }, { 'value': 10, 'label': 'x10' }]);
const ocp_setp_list = [0.078, 0.097, 0.116, 0.135, 0.154, 0.173, 0.192, 0.211, 0.240, 0.269, 0.297, 0.326, 0.354, 0.383, 0.412, 0.440];

const findClosestOcpSetp = (result) => {
  let closestIndex = 0;
  let smallestDifference = Math.abs(result - ocp_setp_list[0]);

  for (let i = 1; i < ocp_setp_list.length; i++) {
    let currentDifference = Math.abs(result - ocp_setp_list[i]);
    if (currentDifference < smallestDifference) {
      smallestDifference = currentDifference;
      closestIndex = i;
    }
  }

  return closestIndex;
}

const handleSubmit = async () => {
  const currentNode = getNode('current');
  const sensorNode = getNode('sensor');
  const amplifierNode = getNode('amplifier');
  const result = currentNode.value * sensorNode.value * amplifierNode.value;

  const index = findClosestOcpSetp(result);

  const formData = {
    current: currentNode.value,
    sensor: sensorNode.value,
    amplifier: amplifierNode.value,
    result: result,
    ocpThreshold: index
  };

  console.log(formData);
  alert(JSON.stringify(formData));
}
</script>

<template>
  <h1>FU5821T OCP Settings</h1>
  <FormKit type="form" @submit="handleSubmit">
    <FormKit type="number" name="current" id="current" label="馬達電流 (A)" number="float" step="any" value="0.0" />
    <FormKit type="select" name="sensor" id="sensor" label="電流感測電阻" :options="sensor_resistor_list" />
    <FormKit type="select" name="amplifier" id="amplifier" label="放大器倍率" :options="amplifier_ratio_list" />
  </FormKit>
  <div>
    <img src="./assets/sch.jpg">
    <img src="./assets/table.jpg">
  </div>
</template>
