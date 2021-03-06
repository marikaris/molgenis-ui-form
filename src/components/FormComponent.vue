<template>
  <vue-form :id="id" :state="state" @submit.prevent="hooks.onSubmit(data)" @reset.prevent="hooks.onCancel">
    <fieldset v-for="field in schema.fields">

      <!-- Render checkbox field -->
      <template v-if="field.type === 'checkbox'">
        <checkbox-field-component
          v-model="data[field.id]"
          :field="field"
          :state="state[field.id]"
          :validate="validate"
          @dataChange="hooks.onValueChanged(data)">
        </checkbox-field-component>
      </template>

      <!-- Render radio field -->
      <template v-else-if="field.type === 'radio'">
        <radio-field-component
          v-model="data[field.id]"
          :field="field"
          :state="state[field.id]"
          :validate="validate"
          @dataChange="hooks.onValueChanged(data)">
        </radio-field-component>
      </template>

      <!-- Render text area field -->
      <template v-else-if="field.type === 'text-area'">
        <text-area-field-component
          v-model="data[field.id]"
          :field="field"
          :state="state[field.id]"
          :validate="validate"
          @dataChange="hooks.onValueChanged(data)">
        </text-area-field-component>
      </template>

      <!-- Render email, url, password, number, and text fields -->
      <template v-else>
        <typed-field-component
          v-model="data[field.id]"
          :field="field"
          :state="state[field.id]"
          :validate="validate"
          @dataChange="hooks.onValueChanged(data)">
        </typed-field-component>
      </template>

    </fieldset>
  </vue-form>
</template>

<script>
  import VueForm from 'vue-form'

  import CheckboxFieldComponent from './field-types/CheckboxFieldComponent'
  import RadioFieldComponent from './field-types/RadioFieldComponent'
  import TextAreaFieldComponent from './field-types/TextAreaFieldComponent'
  import TypedFieldComponent from './field-types/TypedFieldComponent'
  import { FormHook } from '../flow.types'

  export default {
    name: 'FormComponent',
    mixins: [VueForm],
    props: {
      id: {
        type: String,
        required: true
      },
      schema: {
        type: Object,
        required: true,
        validator: (schema) => {
          const fieldIds = new Set()

          const notUnique = schema.fields.some(field => {
            return fieldIds.size === fieldIds.add(field.id).size
          })

          if (notUnique) {
            console.log('Identifiers for fields inside your schema must be unique!')
            return false
          }
          return true
        }
      },
      data: {
        type: Object,
        required: false,
        default: () => ({})
      },
      hooks: {
        type: FormHook,
        required: true
      }
    },
    data () {
      return {
        state: {}
      }
    },
    methods: {
      validate (field) {
        return field.validate(this.data)
      }
    },
    components: {
      CheckboxFieldComponent,
      RadioFieldComponent,
      TextAreaFieldComponent,
      TypedFieldComponent
    }
  }
</script>
