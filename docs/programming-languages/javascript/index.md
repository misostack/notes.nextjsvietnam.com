# Javascript

## Data Validation

### Class Validator

- [Full Example](https://openbase.com/js/class-validator/documentation#validation-messages)

### Class Transformer

#### 1. How to write custom validation decorators

```ts
/* eslint-disable @typescript-eslint/naming-convention */
import {
  registerDecorator,
  ValidationArguments,
  ValidationOptions,
} from "class-validator";

export function IsValidTime(validationOptions?: ValidationOptions) {
  return function (object: Object, propertyName: string) {
    registerDecorator({
      name: "IsValidTime",
      target: object.constructor,
      propertyName: propertyName,
      options: {
        ...validationOptions,
        message: `${propertyName} is not a valid time format. It must be HH:mm:ss. Eg: 17:40:00`,
      },
      validator: {
        validate(value: string, args: ValidationArguments) {
          return value
            ? /(?:[01]\d|2[0123]):(?:[012345]\d):(?:[012345]\d)/.test(value)
            : false;
        },
      },
    });
  };
}
// usage
@property()
@IsValidTime()
timeProp?: string;
```

### Common Regex

1. Time

```js
// Time Format HH:MM 12-hour, optional leading 0
/^(0?[1-9]|1[0-2]):[0-5][0-9]$/
// Time Format HH:MM 12-hour, optional leading 0, Meridiems (AM/PM)
/((1[0-2]|0?[1-9]):([0-5][0-9]) ?([AaPp][Mm]))/
// Time Format HH:MM 24-hour with leading 0
/^(0[0-9]|1[0-9]|2[0-3]):[0-5][0-9]$/
// Time Format HH:MM 24-hour, optional leading 0
/^([0-9]|0[0-9]|1[0-9]|2[0-3]):[0-5][0-9]$/
// Time Format HH:MM:SS 24-hour
/(?:[01]\d|2[0123]):(?:[012345]\d):(?:[012345]\d)/
```
