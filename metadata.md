

```ts
export type ModuleId = "identity-go-test-module";
export type ConfigId = "PM1";

export type ModuleMetadata = { // One Excel Sheet
   moduleId: "identity-go-test-module"
   version: "1.0.0",
   description: "Test Module to be used by GO team for synthetic monitoring",
   keywords: ["Test", "Age", "Data"], 

   category: "Age", // sorting in Module Library

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

export type VariantInfo = {
   configId: "PM1", /** used across all systems to reference this profile as moduleId.configId **/

   name: "Age Verification", /** from excel, en-us primary label **/
   description: "A bar owner wants to ensure that they are not serving alcoholic beverages to under age customer", /** from excel, en-us tooltip/help **/
   
   deprecated?: false | string, /* default false */
   hidden?: false, /* default false, if true, not ever actually displayed, just referenced*/
   constraints?: Constraint[], /* additional constraints passed to compiler/renderer */

   tags: Record<string, string>, // just for selecting which variant in model config pane
   capabilities: Capability[],
   defaultRules: NamedAdviceMap, // MVP = only 1, add a drop-down later

   mainProcessRef: mainTestProcess,  // for compiler only
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


```ts
export type Capability = {
   name:"context.result.advice.age", /* go variable name */
   label:"Age", /* capability name */
   description?:"Age of the person" /* capability description DISPLAY IS NOT MVP: optional tooltip? */
   type: 'integer',
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
