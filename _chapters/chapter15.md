---
title: "Forms and Input Handling in React"
order: 15
chapter_number: 15
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Build** accessible, production-ready forms with React Hook Form
- **Implement** client-side validation with Zod schemas
- **Handle** complex form states and async validation
- **Create** reusable form components with TypeScript
- **Optimize** form performance with proper patterns

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Introduction to React Forms](#introduction-to-react-forms)
- [React Hook Form](#react-hook-form)
  - [Installation and Setup](#installation-and-setup)
  - [Basic Form](#basic-form)
  - [Form Validation](#form-validation)
  - [TypeScript Integration](#typescript-integration)
  - [Error Handling](#error-handling)
- [Advanced Form Patterns](#advanced-form-patterns)
  - [Dynamic Fields](#dynamic-fields)
  - [Field Arrays](#field-arrays)
  - [Conditional Fields](#conditional-fields)
  - [Multi-Step Forms](#multi-step-forms)
- [Input Components](#input-components)
  - [Text Inputs](#text-inputs)
  - [Select and Radio](#select-and-radio)
  - [Checkboxes](#checkboxes)
  - [File Uploads](#file-uploads)
  - [Custom Inputs](#custom-inputs)
- [Validation Strategies](#validation-strategies)
  - [Schema Validation with Zod](#schema-validation-with-zod)
  - [Async Validation](#async-validation)
  - [Custom Validation Rules](#custom-validation-rules)
- [Form State Management](#form-state-management)
  - [Watch and Subscribe](#watch-and-subscribe)
  - [Reset and Default Values](#reset-and-default-values)
  - [Form Context](#form-context)
- [Accessibility](#accessibility)
  - [ARIA Attributes](#aria-attributes)
  - [Error Announcements](#error-announcements)
  - [Keyboard Navigation](#keyboard-navigation)
- [Performance Optimization](#performance-optimization)
  - [Controlled vs Uncontrolled](#controlled-vs-uncontrolled)
  - [Debouncing](#debouncing)
  - [Large Forms](#large-forms)

## Introduction to React Forms

**2025 Form Landscape:**

- **React Hook Form**: Industry standard, performant, minimal re-renders
- **Zod**: TypeScript-first schema validation
- **Formik**: Popular alternative (more React-ish, more re-renders)
- **Uncontrolled forms**: Native HTML with refs (simple use cases)

This chapter focuses on **React Hook Form + Zod**, the modern standard for production applications.

### Why React Hook Form?

1. **Performance**: Minimal re-renders, uses uncontrolled components
2. **Developer Experience**: Simple API, great TypeScript support
3. **Bundle Size**: Smaller than alternatives
4. **Validation**: Built-in support for multiple validation libraries
5. **Accessibility**: Built-in ARIA support

## React Hook Form

### Installation and Setup

```bash
npm install react-hook-form zod @hookform/resolvers
```

### Basic Form

```typescript
// src/components/ContactForm.tsx
import { useForm, SubmitHandler } from 'react-hook-form';

interface ContactFormData {
  name: string;
  email: string;
  message: string;
}

export function ContactForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<ContactFormData>();

  const onSubmit: SubmitHandler<ContactFormData> = async (data) => {
    try {
      const response = await fetch('/api/contact', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      });

      if (!response.ok) throw new Error('Failed to submit');

      alert('Message sent!');
    } catch (error) {
      console.error('Submission error:', error);
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="name">Name</label>
        <input
          id="name"
          type="text"
          {...register('name', { required: 'Name is required' })}
          aria-invalid={errors.name ? 'true' : 'false'}
        />
        {errors.name && (
          <span role="alert" className="error">
            {errors.name.message}
          </span>
        )}
      </div>

      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          {...register('email', {
            required: 'Email is required',
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: 'Invalid email address',
            },
          })}
          aria-invalid={errors.email ? 'true' : 'false'}
        />
        {errors.email && (
          <span role="alert" className="error">
            {errors.email.message}
          </span>
        )}
      </div>

      <div>
        <label htmlFor="message">Message</label>
        <textarea
          id="message"
          rows={5}
          {...register('message', {
            required: 'Message is required',
            minLength: {
              value: 10,
              message: 'Message must be at least 10 characters',
            },
          })}
          aria-invalid={errors.message ? 'true' : 'false'}
        />
        {errors.message && (
          <span role="alert" className="error">
            {errors.message.message}
          </span>
        )}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Sending...' : 'Send Message'}
      </button>
    </form>
  );
}
```

### Form Validation

#### Built-in Validation Rules

```typescript
const {
  register,
  handleSubmit,
  formState: { errors },
} = useForm<FormData>();

<input
  {...register('username', {
    required: 'Username is required',
    minLength: {
      value: 3,
      message: 'Username must be at least 3 characters',
    },
    maxLength: {
      value: 20,
      message: 'Username must be no more than 20 characters',
    },
    pattern: {
      value: /^[a-zA-Z0-9_]+$/,
      message: 'Username can only contain letters, numbers, and underscores',
    },
  })}
/>

<input
  type="number"
  {...register('age', {
    required: 'Age is required',
    min: { value: 18, message: 'Must be at least 18' },
    max: { value: 120, message: 'Must be under 120' },
    valueAsNumber: true, // Convert to number
  })}
/>
```

### TypeScript Integration

```typescript
import { useForm, SubmitHandler } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Define schema
const userSchema = z.object({
  username: z
    .string()
    .min(3, 'Username must be at least 3 characters')
    .max(20, 'Username must be no more than 20 characters')
    .regex(
      /^[a-zA-Z0-9_]+$/,
      'Username can only contain letters, numbers, and underscores'
    ),
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
  confirmPassword: z.string(),
  age: z.number().min(18, 'Must be at least 18'),
  acceptTerms: z.literal(true, {
    errorMap: () => ({ message: 'You must accept the terms' }),
  }),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ['confirmPassword'],
});

// Infer TypeScript type from schema
type UserFormData = z.infer<typeof userSchema>;

export function RegistrationForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
  });

  const onSubmit: SubmitHandler<UserFormData> = async (data) => {
    console.log('Form data:', data);
    // Submit to API
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* Form fields */}
    </form>
  );
}
```

### Error Handling

#### Display Errors

```typescript
import { FieldErrors } from 'react-hook-form';

interface ErrorMessageProps {
  error?: FieldErrors[string];
}

function ErrorMessage({ error }: ErrorMessageProps) {
  if (!error) return null;

  return (
    <span role="alert" className="error-message">
      {error.message as string}
    </span>
  );
}

// Usage
<div>
  <label htmlFor="email">Email</label>
  <input
    id="email"
    {...register('email')}
    aria-invalid={errors.email ? 'true' : 'false'}
    aria-describedby={errors.email ? 'email-error' : undefined}
  />
  {errors.email && (
    <ErrorMessage id="email-error" error={errors.email} />
  )}
</div>
```

#### Global Error Handling

```typescript
const {
  handleSubmit,
  setError,
  formState: { errors },
} = useForm<FormData>();

const onSubmit = async (data: FormData) => {
  try {
    await submitForm(data);
  } catch (error) {
    if (error instanceof APIError) {
      // Set field-specific errors
      if (error.field) {
        setError(error.field, {
          type: 'server',
          message: error.message,
        });
      } else {
        // Set root error for general errors
        setError('root', {
          type: 'server',
          message: error.message,
        });
      }
    }
  }
};

// Display root error
{errors.root && (
  <div className="alert alert-error">
    {errors.root.message}
  </div>
)}
```

## Advanced Form Patterns

### Dynamic Fields

```typescript
import { useForm, useWatch } from 'react-hook-form';

interface ShippingFormData {
  sameAsShipping: boolean;
  shippingAddress: string;
  billingAddress: string;
}

function ShippingForm() {
  const { register, control, formState: { errors } } = useForm<ShippingFormData>({
    defaultValues: {
      sameAsShipping: true,
    },
  });

  // Watch specific field
  const sameAsShipping = useWatch({
    control,
    name: 'sameAsShipping',
  });

  return (
    <form>
      <div>
        <label htmlFor="shipping">Shipping Address</label>
        <input
          id="shipping"
          {...register('shippingAddress', {
            required: 'Shipping address is required',
          })}
        />
      </div>

      <div>
        <label>
          <input
            type="checkbox"
            {...register('sameAsShipping')}
          />
          Billing address same as shipping
        </label>
      </div>

      {!sameAsShipping && (
        <div>
          <label htmlFor="billing">Billing Address</label>
          <input
            id="billing"
            {...register('billingAddress', {
              required: 'Billing address is required',
            })}
          />
        </div>
      )}
    </form>
  );
}
```

### Field Arrays

```typescript
import { useForm, useFieldArray } from 'react-hook-form';

interface TodoFormData {
  todos: { text: string; completed: boolean }[];
}

function TodoListForm() {
  const { register, control, handleSubmit } = useForm<TodoFormData>({
    defaultValues: {
      todos: [{ text: '', completed: false }],
    },
  });

  const { fields, append, remove } = useFieldArray({
    control,
    name: 'todos',
  });

  const onSubmit = (data: TodoFormData) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {fields.map((field, index) => (
        <div key={field.id} className="todo-item">
          <input
            {...register(`todos.${index}.text`, {
              required: 'Todo text is required',
            })}
            placeholder="Enter todo"
          />
          
          <label>
            <input
              type="checkbox"
              {...register(`todos.${index}.completed`)}
            />
            Completed
          </label>

          <button
            type="button"
            onClick={() => remove(index)}
            disabled={fields.length === 1}
          >
            Remove
          </button>
        </div>
      ))}

      <button
        type="button"
        onClick={() => append({ text: '', completed: false })}
      >
        Add Todo
      </button>

      <button type="submit">Save Todos</button>
    </form>
  );
}
```

### Conditional Fields

```typescript
import { useWatch } from 'react-hook-form';
import { z } from 'zod';

// Dynamic schema based on account type
const getAccountSchema = (accountType: string) => {
  const baseSchema = z.object({
    accountType: z.enum(['personal', 'business']),
    email: z.string().email(),
  });

  if (accountType === 'business') {
    return baseSchema.extend({
      companyName: z.string().min(1, 'Company name is required'),
      taxId: z.string().min(1, 'Tax ID is required'),
    });
  }

  return baseSchema;
};

function AccountForm() {
  const { register, control, formState: { errors } } = useForm();
  
  const accountType = useWatch({
    control,
    name: 'accountType',
    defaultValue: 'personal',
  });

  return (
    <form>
      <div>
        <label>Account Type</label>
        <select {...register('accountType')}>
          <option value="personal">Personal</option>
          <option value="business">Business</option>
        </select>
      </div>

      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          {...register('email', {
            required: 'Email is required',
          })}
        />
      </div>

      {accountType === 'business' && (
        <>
          <div>
            <label htmlFor="companyName">Company Name</label>
            <input
              id="companyName"
              {...register('companyName', {
                required: 'Company name is required',
              })}
            />
          </div>

          <div>
            <label htmlFor="taxId">Tax ID</label>
            <input
              id="taxId"
              {...register('taxId', {
                required: 'Tax ID is required',
              })}
            />
          </div>
        </>
      )}
    </form>
  );
}
```

### Multi-Step Forms

```typescript
import { useState } from 'react';
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// Step schemas
const step1Schema = z.object({
  firstName: z.string().min(1, 'First name is required'),
  lastName: z.string().min(1, 'Last name is required'),
});

const step2Schema = z.object({
  email: z.string().email(),
  phone: z.string().min(10, 'Phone number is required'),
});

const step3Schema = z.object({
  address: z.string().min(1, 'Address is required'),
  city: z.string().min(1, 'City is required'),
  zipCode: z.string().min(5, 'Zip code is required'),
});

// Combined schema for final submission
const fullSchema = step1Schema.merge(step2Schema).merge(step3Schema);

type FormData = z.infer<typeof fullSchema>;

function MultiStepForm() {
  const [step, setStep] = useState(1);

  const {
    register,
    handleSubmit,
    trigger,
    formState: { errors },
  } = useForm<FormData>({
    resolver: zodResolver(fullSchema),
    mode: 'onChange',
  });

  const nextStep = async () => {
    let isValid = false;

    if (step === 1) {
      isValid = await trigger(['firstName', 'lastName']);
    } else if (step === 2) {
      isValid = await trigger(['email', 'phone']);
    }

    if (isValid) {
      setStep(step + 1);
    }
  };

  const prevStep = () => {
    setStep(step - 1);
  };

  const onSubmit = async (data: FormData) => {
    console.log('Final submission:', data);
    // Submit to API
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div className="progress">
        Step {step} of 3
      </div>

      {step === 1 && (
        <div>
          <h2>Personal Information</h2>
          <div>
            <label htmlFor="firstName">First Name</label>
            <input
              id="firstName"
              {...register('firstName')}
              aria-invalid={errors.firstName ? 'true' : 'false'}
            />
            {errors.firstName && <span>{errors.firstName.message}</span>}
          </div>

          <div>
            <label htmlFor="lastName">Last Name</label>
            <input
              id="lastName"
              {...register('lastName')}
              aria-invalid={errors.lastName ? 'true' : 'false'}
            />
            {errors.lastName && <span>{errors.lastName.message}</span>}
          </div>
        </div>
      )}

      {step === 2 && (
        <div>
          <h2>Contact Information</h2>
          <div>
            <label htmlFor="email">Email</label>
            <input
              id="email"
              type="email"
              {...register('email')}
              aria-invalid={errors.email ? 'true' : 'false'}
            />
            {errors.email && <span>{errors.email.message}</span>}
          </div>

          <div>
            <label htmlFor="phone">Phone</label>
            <input
              id="phone"
              type="tel"
              {...register('phone')}
              aria-invalid={errors.phone ? 'true' : 'false'}
            />
            {errors.phone && <span>{errors.phone.message}</span>}
          </div>
        </div>
      )}

      {step === 3 && (
        <div>
          <h2>Address</h2>
          <div>
            <label htmlFor="address">Street Address</label>
            <input
              id="address"
              {...register('address')}
              aria-invalid={errors.address ? 'true' : 'false'}
            />
            {errors.address && <span>{errors.address.message}</span>}
          </div>

          <div>
            <label htmlFor="city">City</label>
            <input
              id="city"
              {...register('city')}
              aria-invalid={errors.city ? 'true' : 'false'}
            />
            {errors.city && <span>{errors.city.message}</span>}
          </div>

          <div>
            <label htmlFor="zipCode">Zip Code</label>
            <input
              id="zipCode"
              {...register('zipCode')}
              aria-invalid={errors.zipCode ? 'true' : 'false'}
            />
            {errors.zipCode && <span>{errors.zipCode.message}</span>}
          </div>
        </div>
      )}

      <div className="buttons">
        {step > 1 && (
          <button type="button" onClick={prevStep}>
            Previous
          </button>
        )}

        {step < 3 ? (
          <button type="button" onClick={nextStep}>
            Next
          </button>
        ) : (
          <button type="submit">Submit</button>
        )}
      </div>
    </form>
  );
}
```

## Input Components

### Text Inputs

```typescript
import { forwardRef } from 'react';
import { UseFormRegisterReturn } from 'react-hook-form';

interface TextInputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
  registration?: UseFormRegisterReturn;
}

export const TextInput = forwardRef<HTMLInputElement, TextInputProps>(
  ({ label, error, registration, ...props }, ref) => {
    const id = props.id || props.name;

    return (
      <div className="form-field">
        <label htmlFor={id}>{label}</label>
        <input
          ref={ref}
          id={id}
          aria-invalid={error ? 'true' : 'false'}
          aria-describedby={error ? `${id}-error` : undefined}
          {...registration}
          {...props}
        />
        {error && (
          <span id={`${id}-error`} role="alert" className="error">
            {error}
          </span>
        )}
      </div>
    );
  }
);

// Usage
<TextInput
  label="Username"
  {...register('username')}
  error={errors.username?.message}
/>
```

### Select and Radio

```typescript
interface SelectProps extends React.SelectHTMLAttributes<HTMLSelectElement> {
  label: string;
  options: { value: string; label: string }[];
  error?: string;
  registration?: UseFormRegisterReturn;
}

export function Select({
  label,
  options,
  error,
  registration,
  ...props
}: SelectProps) {
  const id = props.id || props.name;

  return (
    <div className="form-field">
      <label htmlFor={id}>{label}</label>
      <select
        id={id}
        aria-invalid={error ? 'true' : 'false'}
        {...registration}
        {...props}
      >
        <option value="">Select an option</option>
        {options.map((option) => (
          <option key={option.value} value={option.value}>
            {option.label}
          </option>
        ))}
      </select>
      {error && <span role="alert" className="error">{error}</span>}
    </div>
  );
}

// Radio Group
interface RadioGroupProps {
  label: string;
  options: { value: string; label: string }[];
  error?: string;
  registration?: UseFormRegisterReturn;
}

export function RadioGroup({
  label,
  options,
  error,
  registration,
}: RadioGroupProps) {
  return (
    <fieldset className="form-field">
      <legend>{label}</legend>
      {options.map((option) => (
        <label key={option.value} className="radio-label">
          <input
            type="radio"
            value={option.value}
            {...registration}
          />
          {option.label}
        </label>
      ))}
      {error && <span role="alert" className="error">{error}</span>}
    </fieldset>
  );
}
```

### Checkboxes

```typescript
interface CheckboxProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
  registration?: UseFormRegisterReturn;
}

export function Checkbox({
  label,
  error,
  registration,
  ...props
}: CheckboxProps) {
  const id = props.id || props.name;

  return (
    <div className="form-field">
      <label className="checkbox-label">
        <input
          id={id}
          type="checkbox"
          aria-invalid={error ? 'true' : 'false'}
          {...registration}
          {...props}
        />
        {label}
      </label>
      {error && <span role="alert" className="error">{error}</span>}
    </div>
  );
}

// Checkbox Group
interface CheckboxGroupProps {
  label: string;
  options: { value: string; label: string }[];
  error?: string;
  registration?: UseFormRegisterReturn;
}

export function CheckboxGroup({
  label,
  options,
  error,
  registration,
}: CheckboxGroupProps) {
  return (
    <fieldset className="form-field">
      <legend>{label}</legend>
      {options.map((option) => (
        <label key={option.value} className="checkbox-label">
          <input
            type="checkbox"
            value={option.value}
            {...registration}
          />
          {option.label}
        </label>
      ))}
      {error && <span role="alert" className="error">{error}</span>}
    </fieldset>
  );
}
```

### File Uploads

```typescript
import { useState } from 'react';
import { UseFormRegisterReturn } from 'react-hook-form';

interface FileUploadProps {
  label: string;
  accept?: string;
  multiple?: boolean;
  error?: string;
  registration?: UseFormRegisterReturn;
  onChange?: (files: FileList | null) => void;
}

export function FileUpload({
  label,
  accept,
  multiple,
  error,
  registration,
  onChange,
}: FileUploadProps) {
  const [preview, setPreview] = useState<string | null>(null);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const files = e.target.files;
    
    if (files && files[0]) {
      // Create preview for images
      if (files[0].type.startsWith('image/')) {
        const reader = new FileReader();
        reader.onloadend = () => {
          setPreview(reader.result as string);
        };
        reader.readAsDataURL(files[0]);
      }
    }

    onChange?.(files);
  };

  return (
    <div className="form-field">
      <label>{label}</label>
      <input
        type="file"
        accept={accept}
        multiple={multiple}
        {...registration}
        onChange={handleChange}
      />
      {preview && (
        <img src={preview} alt="Preview" className="file-preview" />
      )}
      {error && <span role="alert" className="error">{error}</span>}
    </div>
  );
}

// Usage with React Hook Form
function ProfileForm() {
  const { register, handleSubmit } = useForm();

  const onSubmit = (data: any) => {
    const formData = new FormData();
    
    // Add file if selected
    if (data.avatar && data.avatar[0]) {
      formData.append('avatar', data.avatar[0]);
    }
    
    // Submit formData
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FileUpload
        label="Profile Picture"
        accept="image/*"
        registration={register('avatar')}
      />
    </form>
  );
}
```

### Custom Inputs

```typescript
import { Controller } from 'react-hook-form';
import DatePicker from 'react-datepicker';
import 'react-datepicker/dist/react-datepicker.css';

// Custom date picker integration
function FormWithDatePicker() {
  const { control, handleSubmit } = useForm();

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <Controller
        name="birthDate"
        control={control}
        rules={{ required: 'Birth date is required' }}
        render={({ field, fieldState: { error } }) => (
          <div>
            <label>Birth Date</label>
            <DatePicker
              selected={field.value}
              onChange={field.onChange}
              dateFormat="MM/dd/yyyy"
              placeholderText="Select date"
            />
            {error && <span className="error">{error.message}</span>}
          </div>
        )}
      />
    </form>
  );
}
```

## Validation Strategies

### Schema Validation with Zod

```typescript
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// Complex validation schema
const registrationSchema = z.object({
  username: z
    .string()
    .min(3, 'Username must be at least 3 characters')
    .max(20)
    .regex(/^[a-zA-Z0-9_]+$/, 'Only letters, numbers, and underscores'),
  
  email: z
    .string()
    .email('Invalid email')
    .refine(
      async (email) => {
        // Check if email is available
        const response = await fetch(`/api/check-email?email=${email}`);
        const { available } = await response.json();
        return available;
      },
      { message: 'Email is already taken' }
    ),
  
  password: z
    .string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Must contain uppercase letter')
    .regex(/[a-z]/, 'Must contain lowercase letter')
    .regex(/[0-9]/, 'Must contain number')
    .regex(/[^A-Za-z0-9]/, 'Must contain special character'),
  
  confirmPassword: z.string(),
  
  age: z.coerce
    .number()
    .min(18, 'Must be at least 18')
    .max(120, 'Invalid age'),
  
  acceptTerms: z.literal(true, {
    errorMap: () => ({ message: 'You must accept terms' }),
  }),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ['confirmPassword'],
});

type RegistrationData = z.infer<typeof registrationSchema>;

function RegistrationForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<RegistrationData>({
    resolver: zodResolver(registrationSchema),
  });

  return <form>{/* form fields */}</form>;
}
```

### Async Validation

```typescript
// Debounced async validation
import { useForm } from 'react-hook-form';
import { debounce } from 'lodash';
import { useCallback } from 'react';

function UsernameForm() {
  const { register, setError, clearErrors } = useForm();

  const checkUsername = useCallback(
    debounce(async (username: string) => {
      if (username.length < 3) return;

      try {
        const response = await fetch(`/api/check-username?username=${username}`);
        const { available } = await response.json();

        if (!available) {
          setError('username', {
            type: 'manual',
            message: 'Username is already taken',
          });
        } else {
          clearErrors('username');
        }
      } catch (error) {
        console.error('Validation error:', error);
      }
    }, 500),
    []
  );

  return (
    <input
      {...register('username', {
        required: 'Username is required',
        onChange: (e) => checkUsername(e.target.value),
      })}
    />
  );
}
```

### Custom Validation Rules

```typescript
// Custom validation functions
const isStrongPassword = (password: string): boolean => {
  const hasUpperCase = /[A-Z]/.test(password);
  const hasLowerCase = /[a-z]/.test(password);
  const hasNumbers = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
  
  return hasUpperCase && hasLowerCase && hasNumbers && hasSpecialChar;
};

const isValidPhoneNumber = (phone: string): boolean => {
  const phoneRegex = /^\+?1?\d{9,15}$/;
  return phoneRegex.test(phone.replace(/[\s()-]/g, ''));
};

// Usage
<input
  {...register('password', {
    required: 'Password is required',
    validate: {
      strong: (value) =>
        isStrongPassword(value) ||
        'Password must contain uppercase, lowercase, number, and special character',
    },
  })}
/>

<input
  {...register('phone', {
    required: 'Phone is required',
    validate: {
      validFormat: (value) =>
        isValidPhoneNumber(value) || 'Invalid phone number format',
    },
  })}
/>
```

## Form State Management

### Watch and Subscribe

```typescript
import { useForm, useWatch } from 'react-hook-form';
import { useEffect } from 'react';

function PricingForm() {
  const { register, control, watch } = useForm({
    defaultValues: {
      quantity: 1,
      price: 10,
    },
  });

  // Watch specific fields
  const quantity = useWatch({ control, name: 'quantity' });
  const price = useWatch({ control, name: 'price' });

  // Watch all fields
  const allValues = watch();

  // Subscribe to specific field changes
  useEffect(() => {
    const subscription = watch((value, { name, type }) => {
      console.log('Field changed:', name, 'New value:', value);
    });

    return () => subscription.unsubscribe();
  }, [watch]);

  const total = quantity * price;

  return (
    <form>
      <div>
        <label>Quantity</label>
        <input
          type="number"
          {...register('quantity', { valueAsNumber: true })}
        />
      </div>

      <div>
        <label>Price</label>
        <input
          type="number"
          {...register('price', { valueAsNumber: true })}
        />
      </div>

      <div>
        <strong>Total: ${total}</strong>
      </div>
    </form>
  );
}
```

### Reset and Default Values

```typescript
import { useForm } from 'react-hook-form';
import { useEffect } from 'react';

interface UserFormData {
  name: string;
  email: string;
}

function EditUserForm({ userId }: { userId: string }) {
  const {
    register,
    handleSubmit,
    reset,
    formState: { isDirty, isSubmitSuccessful },
  } = useForm<UserFormData>({
    defaultValues: {
      name: '',
      email: '',
    },
  });

  // Load user data and set as form values
  useEffect(() => {
    async function loadUser() {
      const response = await fetch(`/api/users/${userId}`);
      const user = await response.json();
      
      // Reset form with fetched data
      reset({
        name: user.name,
        email: user.email,
      });
    }

    loadUser();
  }, [userId, reset]);

  // Reset after successful submission
  useEffect(() => {
    if (isSubmitSuccessful) {
      reset(); // Reset to default values
    }
  }, [isSubmitSuccessful, reset]);

  const onSubmit = async (data: UserFormData) => {
    await fetch(`/api/users/${userId}`, {
      method: 'PUT',
      body: JSON.stringify(data),
    });
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      <input {...register('email')} />
      
      <button type="submit" disabled={!isDirty}>
        Save Changes
      </button>
      
      <button
        type="button"
        onClick={() => reset()}
        disabled={!isDirty}
      >
        Cancel
      </button>
    </form>
  );
}
```

### Form Context

```typescript
import { useForm, FormProvider, useFormContext } from 'react-hook-form';

// Child component using form context
function NameFields() {
  const {
    register,
    formState: { errors },
  } = useFormContext<FormData>();

  return (
    <div>
      <input {...register('firstName')} />
      {errors.firstName && <span>{errors.firstName.message}</span>}
      
      <input {...register('lastName')} />
      {errors.lastName && <span>{errors.lastName.message}</span>}
    </div>
  );
}

// Parent component
function RegistrationForm() {
  const methods = useForm<FormData>();

  return (
    <FormProvider {...methods}>
      <form onSubmit={methods.handleSubmit(onSubmit)}>
        <NameFields />
        {/* Other form sections */}
      </form>
    </FormProvider>
  );
}
```

## Accessibility

### ARIA Attributes

```typescript
function AccessibleForm() {
  const {
    register,
    formState: { errors },
  } = useForm();

  return (
    <form aria-label="Registration form">
      <div>
        <label htmlFor="email">
          Email
          <span aria-label="required">*</span>
        </label>
        <input
          id="email"
          type="email"
          {...register('email', {
            required: 'Email is required',
          })}
          aria-required="true"
          aria-invalid={errors.email ? 'true' : 'false'}
          aria-describedby={errors.email ? 'email-error' : undefined}
        />
        {errors.email && (
          <span id="email-error" role="alert" aria-live="polite">
            {errors.email.message}
          </span>
        )}
      </div>
    </form>
  );
}
```

### Error Announcements

```typescript
import { useEffect, useRef } from 'react';

function FormWithAnnouncements() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitSuccessful },
  } = useForm();

  const announcementRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (Object.keys(errors).length > 0) {
      const errorCount = Object.keys(errors).length;
      if (announcementRef.current) {
        announcementRef.current.textContent = 
          `Form contains ${errorCount} error${errorCount > 1 ? 's' : ''}. Please review and correct.`;
      }
    } else if (isSubmitSuccessful) {
      if (announcementRef.current) {
        announcementRef.current.textContent = 'Form submitted successfully!';
      }
    }
  }, [errors, isSubmitSuccessful]);

  return (
    <>
      <div
        ref={announcementRef}
        role="status"
        aria-live="polite"
        aria-atomic="true"
        className="sr-only"
      />
      <form onSubmit={handleSubmit(onSubmit)}>
        {/* form fields */}
      </form>
    </>
  );
}
```

### Keyboard Navigation

```typescript
// Ensure proper tab order and keyboard interaction
function KeyboardAccessibleForm() {
  return (
    <form>
      {/* Standard inputs have natural tab order */}
      <input type="text" tabIndex={0} />
      
      {/* Custom components need keyboard support */}
      <button
        type="button"
        onKeyDown={(e) => {
          if (e.key === 'Enter' || e.key === ' ') {
            e.preventDefault();
            // Handle action
          }
        }}
      >
        Custom Button
      </button>

      {/* Skip navigation for better UX */}
      <a href="#main-content" className="skip-link">
        Skip to main content
      </a>
    </form>
  );
}
```

## Performance Optimization

### Controlled vs Uncontrolled

```typescript
// ✅ Uncontrolled (better performance)
function UncontrolledForm() {
  const { register, handleSubmit } = useForm();

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} />
    </form>
  );
}

// ⚠️ Controlled (more re-renders, use only when necessary)
function ControlledForm() {
  const { control } = useForm();

  return (
    <Controller
      name="username"
      control={control}
      render={({ field }) => (
        <input
          value={field.value}
          onChange={field.onChange}
        />
      )}
    />
  );
}
```

### Debouncing

```typescript
import { useCallback } from 'react';
import { debounce } from 'lodash';

function SearchForm() {
  const { register } = useForm();

  const handleSearch = useCallback(
    debounce(async (query: string) => {
      const results = await fetch(`/api/search?q=${query}`);
      // Update results
    }, 300),
    []
  );

  return (
    <input
      {...register('search')}
      onChange={(e) => handleSearch(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

### Large Forms

```typescript
import { memo } from 'react';

// Memoize individual field components
const MemoizedField = memo(function Field({ name, label }: FieldProps) {
  const { register } = useFormContext();

  return (
    <div>
      <label>{label}</label>
      <input {...register(name)} />
    </div>
  );
});

// Use in large form
function LargeForm() {
  const methods = useForm();

  return (
    <FormProvider {...methods}>
      <form>
        {/* These won't re-render unless their props change */}
        <MemoizedField name="field1" label="Field 1" />
        <MemoizedField name="field2" label="Field 2" />
        {/* ... many more fields */}
      </form>
    </FormProvider>
  );
}
```

## Summary and Best Practices

### 2025 Form Guidelines

**✅ Recommended Practices:**

1. **Use React Hook Form** - Industry standard, performant
2. **Validate with Zod** - Type-safe, reusable schemas
3. **Make forms accessible** - ARIA attributes, error announcements
4. **Provide immediate feedback** - Validate on blur/change
5. **Handle loading states** - Disable submit during submission
6. **Use TypeScript** - Type-safe form data
7. **Optimize performance** - Uncontrolled components, debouncing

**❌ Avoid These Patterns:**

1. **Don't use controlled inputs unnecessarily** - Causes excessive re-renders
2. **Don't skip accessibility** - Always include labels and ARIA
3. **Don't validate only on submit** - Provide real-time feedback
4. **Don't forget error handling** - Handle both client and server errors
5. **Don't expose sensitive data** - Never log passwords or tokens

### Next Steps

Now that you master forms, continue with:

- **Chapter 16**: Testing React Applications
- **Chapter 17**: Deployment and Production

Forms are the backbone of user interaction - build them well!
