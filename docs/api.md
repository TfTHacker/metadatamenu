# Api

API is accessible with `app.plugins.plugins["metadata-menu"].api`

### getValues (deprecated)

`getValues(fileOrFilePath: TFile | string, attribute: string)`

Takes a TFile containing the field and a string for the related field name
Returns an array with the values of the field. If there are several fields with the same name (in object list fields for example, see [Settings](settings#object-listoptions)), this function will return an array with all the exisiting values

This is an asynchronous function, so you should await it.

### getValuesForIndexedPath

`getValuesForIndexedPath(fileOrFilePath: TFile | string, indexedPath: string)`

Takes a TFile containing the field and a string for the related field's [indexedPath](fileclasses#id-and-indexedpath)

Returns the value of the field for this indexedPath

This is an asynchronous function, so you should await it.

### postValues
creates or updates fields with values in the target note
`postValues(fileOrFilePath: TFile | string, payload: FieldsPayload, lineNumber?: number, after?: boolean, asList?: boolean, asBlockquote?:boolean)`

#### parameters:

- `fileOrFilePath: TFile | string` : the target file where to create or update the fields
- `payload: FieldsPayload`: list of fields and values to create or update (see type definition below) 
- `lineNumber?: number` : optional line number where to create fields if it doesn't exist. If the field already exists, this attribute won't do anything. If line number is undefined and the field doesn't exist yet, it will be included in frontmatter
- `after?: boolean` : optional parameter to create new fields after or before the line number. Defaults to `true`
- `asList?: boolean`: optional parameter to create new fields as list (insert a `- ` before the field's name) . Defaults to `false`
- `asBlockquote?: boolean`: optional parameter to create new fields as comment (insert a `>` before the field's name) . Defaults to `false`

#### `FieldsPayload` and `FieldPayload`

```typescript
export type FieldPayload = {
    value: string, // the field's value as string
}

export type FieldsPayload = Array<{
    id: string, //the indexedPath of the field
    payload: FieldPayload
}>
```

### fieldModifier
`fieldModifier(dv: any, p: any, fieldName: string, attrs?: { cls: string, attr: Record<string, string> })`

Takes a dataview api instance, a page, a field name and optional attributes and returns a HTML element to modify the value of the field in the target note

### fileFields
`fileFields(fileOrFilePath: TFile | string)`

Takes a TFile or e filePath and returns all the fields in the document, both frontmatter and dataview fields, and returns a collection of analysis of those fields by metadatamenu:

```typescript
{
    (indexedPath: string): {
        /* the value of the field in the file */
        value: string | undefined, 

        /* the fileClass name applied to this field if there is a fileClass AND if the field is set in the fileClass or the fileClass it's inheriting from */
        fileClassName: string | undefined,

        /* true if this fieldName is in "Globally ignored fields" in the plugin settings */
        ignoreInMenu: boolean | undefined,

        /* true if this field as a setting defined in the plugin settings or a fileClass and if the value is valid according to those settings */
        isValid: boolean | undefined,

        /* an object containing the options available for this field according to the plugin settings or the fileClass */
        options: Record<string, string> | undefined,

        /* wether the settings applied to this field come from a fileClass, the plugin settings or none  */
        sourceType: "fileClass" | "settings" | undefined,

        /* the type of the field according to the plugin settings or the fileClass  */
        type: "Input" | "Select" | "Multi" | "Cycle" | "Boolean" | "Number" | "File" | "MultiFile" | "Date" | "Lookup" | "Formula" | "Canvas" | "CanvasGroup" | "CanvasGroupLink" | "YAML" | "JSON" | "Object" | "ObjectList"

        /* the unique identifier of the field definition in the vault */
        id: string

        /* the unique idenfier of the path of this field in this file*/
        indexedPath: string | undefined
    }
}
```

### insertMissingFields
`insertMissingFields: (fileOrFilePath: string | TFile, lineNumber: number, boolean, asList: boolean, asBlockquote: boolean, fileClassName?: string)`

Takes:
- a TFile or its path, 
- a line number,
- asks wether insertion is in after the line (default : false),
- asks wether insertion is as list (prepends `- `) (default : false),
- asks wether insertion is as comment (prepends `>`)  (default : false),
- asks wether insertion should only deal with one single fileClass' fields (default: all)

Inserts all missings fields of all (or one specified) fileClass fields at the line, with the format

This is an asynchronous function, so you should await it.
