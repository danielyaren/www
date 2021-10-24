+++
title = "Composing Object Schemas with Yup"
description = "Composing Object Schemas with Yup"
[extra]
image = "favicon.ico"
header_color = "bg-blue"
text_color = "near-black"
alt_text_color = "black"
hover_color = "hover-blue"
+++

I've been working on a React project consisting of 6 different forms. One of my main goals in this project is to make the development of a form extremely rapid. The idea is to compose each form by bringing in valid schemas for logical form groups, which handles object validation and validation messages, and then hooking them up with generic components for all of the different types of inputs.

Logical schema groups were created by analysing the commonalities between each form:

```javascript
const personSchema = Yup.object({
    title: Yup.string().required();
    firstName: Yup.string().required();
    lastName: Yup.string().required();
});

const occupationSchema = Yup.object({
    occupation: Yup.string().required();
    partTime: Yup.bool().required();
})
```

Then it's a simple case of creating an overall schema for each form:

```javascript
const motorcycleSchema = Yup.object({
    ...personSchema
    ...occupationSchema
}).required();

const motorhomeSchema = Yup.object({
    ...personSchema
    belongsToCaravanClub: Yup.bool().required();
});

export { motorcycleSchema }

```

Hook up the schema to your form (in this case, react-hook-form, which is amazing ❤️)

```javascript
const { register, handleSubmit, formState:{ errors } } = useForm({
    resolver: yupResolver(motorcycleSchema)
});
```
Done!