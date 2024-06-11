# Module Metadata Specification

Module metadata is used by the Go Platform Designer to render various UI components, and by the Compiler to translate the Module into BPMN. 

Module Metadata MUST pass the Module conformance test suite schema and semantic validation. 

## Metadata Overview 

```ts
export type ModuleId = "identity-go-test-module";
export type ConfigId = "PM1";

export type ModuleMetadata = { // One Excel Sheet
   moduleId: "identity-go-test-module"
   version: "1.0.0",
   description: "Test Module to be used by GO team for synthetic monitoring",
   keywords: ["Test", "Age", "Data"], 

   category: "Age", // sorting in Module Library

   display: PresentationInfo,
   display_stack: PresentationInfo, // MVP+

   selectors: [], // for drawing dropdowns. default is to use the name
   defaultConfigId: "PM1",
   variantSelector?: "GLOBAL", /* for multicountry only */ 
   variants: ["Age Verification"],
   files: Record<FileName, FileData>,
   base64File: string,
   zipFile: Base64Data, // TBD
   i18n: I18NLanguages, // all displayed strings are searched here for translations
   hidden: string[], 
}

type PresentationInfo = {
   label: "", // en-us primary label
   description: "", // en-us tooltip/info
   icon?: "lightbulb",
   iconColor?: CssColor,
   nodeShape?: string,
   
}

type SelectorSpec = {
   tag?: "", /* if present, then a tag. if absent, then a name */
   label?: "", /* if present, then rename the tag for display. if absent, use the tag, or literal 'name' */
   default: string, // value to use upon initialization

   // for use only by Go Platform team. reserved for MVP+
   exclusiveGatewayEdgeCondition?: string
}
```

## General Module Information




### Module Selectors


#### Predefined Tags:


## Module Variants

The Module bundle contains one or more Variants as an array of `ModelConfig`. 
```ts
export type VariantInfo = {
   configId: "PM1", /** used across all systems to reference this profile as moduleId.configId **/

   name: "Age Verification", /** from excel, en-us primary label **/
   description: "A bar owner wants to ensure that they are not serving alcoholic beverages to under age customer", /** from excel, en-us tooltip/help **/
   
   deprecated?: boolean | string, /* default false */
   hidden?: boolean, /* default false, if true, not ever actually displayed, just referenced*/
   constraints?: Constraint[], /* additional constraints passed to compiler/renderer */

   tags: Record<string, string>, // just for selecting which variant in model config pane
   capabilities: Capability[],
   defaultRules: NamedAdviceMap, // MVP = only 1, add a drop-down later

   mainProcessRef: "mainTestProcess",  // for compiler only
   configFormRef: GoTestConfigurationInput, 
   credentialFormRef: GoTestCredentialInputForm, // TODO for the UI (eventually), but also for the compiler
   inputFormRef: GoTestInformationInputForm,  // MVP+ to support field lifting

   // MVP+ enhancement
   credentialCheckProcessRef: FlowableActivityReference, // check a credential
   skus?: string[], // for implicit billing
   env: Record<string, string>, /* additional configuration injection */
   payload?: object, /* default {}. allows re-use of config form with alternative configs*/
   regions: EnabledRegionInfo,
   consent: Consent[]
} 

export type NamedAdviceMap = {
    name: string,
    description: string,
    rules: OutcomeGroupSpec[]
}

```

The point of tags is to allow multiple dropdowns to be specified in the sidebar, 
with a dropdown for each (as specified in the `selectors`)


```yml
tags:
  displayName: "Simple USA Address+SSN+DOB" 
  country: USA
  variation: Simple
  inputs: Address+SSN+DOB
```

### Constraints

Constraints concern the placement of Modules on the canvas, and conditions which the compiler can check for. 

Currently, the following constraints are defined:

- `writes:identity` - this constraint indicates that the module writes to the identity space. Only one such module may execute at a time. 
- `reads:identity.name` - this constraint indicates that the module reads from the normalized name field, meaning that the name must be present and normalized prior to processing

## Credentials

Each Module Config Variant *may* have zero or more credentials associated with it. 

Go Platform associates a credentials form payload with the following dimensions:

- `ORG_ID`: the customer or organization, implicit in credential assignment
- `GO_REGION`: varies, but probably 'US', 'EU', 'AP'
- `PROD_MODE`: either 'PRD' or 'STG'
- `ModuleId.ConfigId`: defined here



## Capabilities (Flags, Matches, and Scores)

```ts
export type Capability = {
   name:"context.result.advice.age", /* go variable name */
   label:"Age", /* capability name */
   description?:"Age of the person" /* capability description DISPLAY IS NOT MVP: optional tooltip? */
   type: 'integer' | 'boolean' | 'enum' | 'enumset',
   defaultValue: ValueType /** may be array for multiselect */,
   values?: EnumValueEntry[]  /* Only when type is enum or enumset */
   range?: {min: number, max: integer} /* Later, NOT MVP */
}

export type EnumValueEntry = {
      name: string,
      label: string
      description?:string /* NOT MVP: optional tooltip? */
}

//
- name: Decision Band
  description: This is stuff about the band
  values: 
    - name: no_matches
      label: No Matches
    - name: name_address_1x
      label: 1 x Name and Address 

<select>
<option value="no_matches">No Matches</option>
..
</select>





export type Capability = {
   name:string,
   description:string,
   values: {
      value: string,
      label: string
   }
}

```

### Boolean Groups and Enumerated String Equivalence

A group of boolean capabilities is functionally equivalent and lossless translatable to an enumerated set. 
A one-hot group of boolean capabilities is functionally equivalent and lossless translatable to an enumerated set.

As specification of a boolean is more complete, use one entry per line but with the notation `'enumerated_element'` or `'enumerated_set_element'` to build the group of enumerated values, rather than using carriage return character in excel. All entries should have the same variable name. 

The Go Module Conformance Suite can map these into the correct format, while preserving notes and defaults. 

## Outcomes (Advice Templates)

## Non-Functional Data

### Billing SKUs

### Terms and Conditions

Some forms of processing require legal terms and conditions to be agreed to by 
the End Users. 


### Support and Status Page

When there is an issue with a module, Go Platform can return a support message 
indicating what they should provide to tier-1 support. 

```ts

interface SupportInfo {
    message: string, 
}

```
