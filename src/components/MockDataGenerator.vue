<template>
  <div class="p-6 max-w-4xl mx-auto space-y-6">
    <h1 class="text-3xl font-bold text-center text-gray-800">Mock Data Generator</h1>

    <!-- Toggle -->
    <div class="flex justify-center gap-6 items-center">
      <label class="inline-flex items-center gap-2">
        <input
            type="radio"
            value="form"
            v-model="mode"
            class="accent-blue-600"
        />
        <span :class="mode === 'form' ? 'font-bold text-blue-600' : 'text-gray-700'">Form Mode</span>
      </label>

      <label class="inline-flex items-center gap-2">
        <input
            type="radio"
            value="interface"
            v-model="mode"
            class="accent-blue-600"
        />
        <span :class="mode === 'interface' ? 'font-bold text-blue-600' : 'text-gray-700'">Interface Mode</span>
      </label>
    </div>


    <!-- Data Count -->
    <div class="flex justify-center items-center gap-2">
      <label class="font-semibold text-gray-700">Number of Records:</label>
      <input
          type="number"
          min="1"
          v-model.number="dataCount"
          class="border border-gray-300 p-2 rounded w-24 text-center"
      />
    </div>

    <!-- Interface Input Mode -->
    <div v-if="useInterfaceMode">
      <textarea
          v-model="interfaceText"
          rows="10"
          placeholder="Paste your TypeScript interface here"
          class="w-full p-4 border border-gray-300 rounded font-mono"
      ></textarea>
    </div>

    <!-- Form Mode -->
    <div v-else>
      <div v-for="(field, index) in fields" :key="index" class="flex gap-2 items-center mb-2">
        <input
            v-model="field.name"
            placeholder="Field name"
            class="border border-gray-300 p-2 rounded w-full sm:w-1/2"
        />
        <select
            v-model="field.type"
            class="border border-gray-300 p-2 rounded w-full sm:w-1/2"
        >
          <option disabled value="">Select Type</option>
          <option v-for="(label, key) in typeOptions" :key="key" :value="key">
            {{ label }}
          </option>
        </select>
        <button @click="removeField(index)" class="text-red-600 hover:text-red-800 text-lg">🗑️</button>
      </div>
      <button @click="addField" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 mt-2">+ Add Field
      </button>
    </div>

    <button @click="generateData" class="bg-green-600 text-white px-6 py-2 rounded hover:bg-green-700">Generate Data
    </button>

    <div v-if="generatedData" class="bg-gray-100 rounded p-4 mt-6">
      <div class="flex justify-between items-center mb-2">
        <h2 class="text-xl font-semibold">Generated JSON:</h2>
        <button @click="copyToClipboard" class="text-blue-600 hover:underline">📋 Copy</button>
      </div>
      <pre class="whitespace-pre-wrap text-sm">{{ generatedData }}</pre>
    </div>
  </div>
</template>

<script setup>
import {computed, ref} from 'vue'
import {faker} from '@faker-js/faker'

const mode = ref('form')
const useInterfaceMode = computed(() => mode.value === 'interface')
const interfaceText = ref('')
const dataCount = ref(1)

const fields = ref([
  {name: '', type: ''}
])

// 1. Faker mapping
const typeMap = {
  name: () => faker.person.fullName(),
  firstName: () => faker.person.firstName(),
  lastName: () => faker.person.lastName(),
  username: () => faker.internet.userName(),
  email: () => faker.internet.email(),
  phone: () => faker.phone.number(),
  address: () => faker.location.streetAddress(),
  city: () => faker.location.city(),
  country: () => faker.location.country(),
  zipCode: () => faker.location.zipCode(),
  date: () => faker.date.past().toISOString(),
  birthdate: () => faker.date.birthdate().toISOString(),
  company: () => faker.company.name(),
  jobTitle: () => faker.person.jobTitle(),
  lorem: () => faker.lorem.words(5),
  paragraph: () => faker.lorem.paragraph(),
  number: () => faker.number.int({min: 1, max: 1000}),
  uuid: () => faker.string.uuid(),
  imageUrl: () => faker.image.url(),
  url: () => faker.internet.url(),
  ip: () => faker.internet.ip(),
  password: () => faker.internet.password(),
}

// 2. Label map (typeOptions আগের মতোই রাখলে ভালো, ইউজারের বোধগম্যতার জন্য)
const typeOptions = {
  name: 'Full Name',
  firstName: 'First Name',
  lastName: 'Last Name',
  username: 'Username',
  email: 'Email',
  phone: 'Phone Number',
  address: 'Street Address',
  city: 'City',
  country: 'Country',
  zipCode: 'Zip Code',
  date: 'Date (past)',
  birthdate: 'Birthdate',
  company: 'Company Name',
  jobTitle: 'Job Title',
  lorem: 'Random Text',
  paragraph: 'Paragraph',
  number: 'Random Number',
  uuid: 'UUID',
  imageUrl: 'Image URL',
  url: 'URL',
  ip: 'IP Address',
  password: 'Password',
}

// 3. Record generator
function createOneRecord(fieldList) {
  const item = {}
  fieldList.forEach(field => {
    if (!field.name || !field.type) return
    const generator = typeMap[field.type]
    item[field.name] = generator ? generator() : null
  })
  return item
}

const generatedData = ref('')

function addField() {
  fields.value.push({name: '', type: ''})
}

function removeField(index) {
  fields.value.splice(index, 1)
}

function parseInterface(interfaceString) {
  const regex = /(\w+)\s*:\s*['"]?([\w\[\]]+)['"]?/g
  const result = []
  let match
  while ((match = regex.exec(interfaceString)) !== null) {
    const [_, name, type] = match
    // Use guessTypeFromTS to better identify the type
    let detectedType = guessTypeFromTS(type)
    result.push({name, type: detectedType})
  }
  return result
}

function mapCustomTypeToFaker(type) {
  const customTypeMap = {
    'currency': 'number',
    'idNumber': 'uuid',
    'sentence': 'paragraph'
  }

  return customTypeMap[type] || (typeMap[type] ? type : 'lorem')
}

function guessTypeFromTS(type) {
  // Improved type detection
  if (type.toLowerCase() === 'date') return 'date'
  if (type.toLowerCase() === 'number') return 'number'
  if (type.includes('string') && type.toLowerCase().includes('date')) return 'date'
  if (type.includes('string') && type.toLowerCase().includes('email')) return 'email'
  if (type.includes('string') && type.toLowerCase().includes('name')) return 'name'
  if (type.includes('string') && type.toLowerCase().includes('ip')) return 'ip'
  if (type.includes('id') || type.toLowerCase().includes('card')) return 'uuid'

  // Check if the type is directly in our typeMap
  return typeMap[type.toLowerCase()] ? type.toLowerCase() : 'lorem'
}

function generateData() {
  let fieldList = []

  if (useInterfaceMode.value) {
    fieldList = parseInterface(interfaceText.value)
  } else {
    fieldList = fields.value
  }

  const all = Array.from({length: dataCount.value}, () => createOneRecord(fieldList))
  generatedData.value = JSON.stringify(dataCount.value === 1 ? all[0] : all, null, 2)
}


function copyToClipboard() {
  navigator.clipboard.writeText(generatedData.value).then(() => {
    alert('Copied to clipboard!')
  })
}
</script>
