# Steps to create custom preset
Create preview files in
```
src/components/editor/previews/SliderPreview.tsx
```
```ts
import React from 'react'
import { useInteractive } from '~hooks/useInteractive'
import ComponentPreview from '~components/editor/ComponentPreview'
import { Slider } from '@chakra-ui/react'
const SliderPreview: React.FC<IPreviewProps> = ({ component }) => {
  const { props } = useInteractive(component, false)
  return (
    <Slider {...props}>
      {component.children.map((key: string) => (
        <ComponentPreview key={key} componentName={key} />
      ))}
    </Slider>
  )
}
export default SliderPreview
```
```
src/components/editor/previews/SliderTrackPreview.tsx
```
```ts
import React from 'react'
import { useInteractive } from '~hooks/useInteractive'
import ComponentPreview from '~components/editor/ComponentPreview'
import { SliderTrack } from '@chakra-ui/react'
const SliderTrackPreview: React.FC<IPreviewProps> = ({ component }) => {
  const { props } = useInteractive(component, false)
  return (
    <SliderTrack {...props}>
      {component.children.map((key: string) => (
        <ComponentPreview key={key} componentName={key} />
      ))}
    </SliderTrack>
  )
}
export default SliderTrackPreview
```
Then import in ComponentPreview.tsx
```ts
import SliderTrackPreview from './previews/SliderTrackPreview'
import SliderPreview from './previews/SliderPreview'
// For basic components
case 'SliderThumb':
case 'SliderFilledTrack':
  // For complex components
  case 'SliderTrack':
    return <SliderTrackPreview component={component} />
  case 'Slider':
    return <SliderPreview component={component} />
```
Create panel in components
```
src/components/inspector/panels/components/SliderPanel.tsx
```
```ts
import React, { memo } from 'react'
import {
  SliderTrack,
  SliderFilledTrack,
  Slider,
  SliderThumb,
  Select,
  Input,
} from '@chakra-ui/react'
import FormControl from '~components/inspector/controls/FormControl'
import { useForm } from '~hooks/useForm'
import usePropsSelector from '~hooks/usePropsSelector'
import SizeControl from '~components/inspector/controls/SizeControl'
import ColorsControl from '~components/inspector/controls/ColorsControl'
const SliderPanel = () => {
  const { setValue, setValueFromEvent } = useForm()
  const value = usePropsSelector('value')
  const size = usePropsSelector('size')
  const step = usePropsSelector('step')
  const orientation = usePropsSelector('orientation')
  const label = usePropsSelector('aria-label')
  const min = usePropsSelector('min')
  const max = usePropsSelector('max')
  return (
    <>
      <FormControl label="Value">
        <Slider
          onChange={value => setValue('value', value)}
          min={0}
          max={100}
          step={1}
          value={value}
          defaultValue={value}
        >
          <SliderTrack>
            <SliderFilledTrack />
          </SliderTrack>
          <SliderThumb />
        </Slider>
      </FormControl>
      <FormControl label="Min">
        <Slider
          onChange={value => setValue('min', value)}
          min={0}
          max={100}
          step={1}
          value={min}
          defaultValue={min}
        >
          <SliderTrack>
            <SliderFilledTrack />
          </SliderTrack>
          <SliderThumb />
        </Slider>
      </FormControl>
      <FormControl label="Max">
        <Slider
          onChange={value => setValue('max', value)}
          min={0}
          max={100}
          step={1}
          value={max}
          defaultValue={max}
        >
          <SliderTrack>
            <SliderFilledTrack />
          </SliderTrack>
          <SliderThumb />
        </Slider>
      </FormControl>
      <FormControl label="Step">
        <Slider
          onChange={step => setValue('step', step)}
          min={1}
          max={100}
          step={1}
          value={step}
          defaultValue={step}
        >
          <SliderTrack>
            <SliderFilledTrack />
          </SliderTrack>
          <SliderThumb />
        </Slider>
      </FormControl>
      <FormControl label="Aria Label">
        <Input
          size="sm"
          value={label || ''}
          type="text"
          name="aria-label"
          onChange={setValueFromEvent}
        />
      </FormControl>
      <ColorsControl label="Color Scheme" name="colorScheme" />
      <SizeControl label="Size" options={['sm', 'md', 'lg']} value={size} />
      <FormControl htmlFor="orientation" label="Orientation">
        <Select
          id="orientation"
          onChange={setValueFromEvent}
          name="orientation"
          size="sm"
          value={orientation || ''}
        >
          <option>horizontal</option>
          <option>vertical</option>
        </Select>
      </FormControl>
    </>
  )
}
export default memo(SliderPanel)
```
Then insert component file in panel
```ts
import SliderPanel from './components/SliderPanel'
// type
{
  type === 'Slider' && <SliderPanel />
}
{
  type === 'SliderTrack' && <ChildrenControl />
}
{
  type === 'SliderFilledTrack' && <ChildrenControl />
}
{
  type === 'SliderThumb' && <ChildrenControl />
}
```
Now add it to component.tsx list
```ts
 Slider: {
    children: {
      SliderTrack: {},
      SliderFilledTrack: {},
      SliderThumb: {},
    },
  },
```
Add to builder.ts
```ts
export const buildSlider = (parent: string): ComposedComponent => {
  const composer = new Composer()
  const nodeId = composer.addNode({ type: 'Slider' })
  const track = composer.addNode({ type: 'SliderTrack', parent: nodeId })
  composer.addNode({ type: 'SliderFilledTrack', parent: track })
  composer.addNode({ type: 'SliderThumb', parent: nodeId })
  const components = composer.getComponents()
  return {
    components,
    root: nodeId,
    parent,
  }
}
```
then add to component type
```ts
;(src / react - app - env.d.ts) |
  'Slider' |
  'SliderTrack' |
  'SliderFilledTrack' |
  'SliderThumb'
```
Add to utils
```ts
// src/utils/defaultProps.tsx
SliderProps,
  SliderTrackProps,
  SliderThumbProps,
  }
  //
   Slider?: PropsWithForm<SliderProps>
  SliderTrack?: PropsWithForm<SliderTrackProps>
  SliderFilledTrack?: PropsWithForm<any>
  SliderThumb?: PropsWithForm<SliderThumbProps>
  //
   Slider: {
    step: 1,
    size: 'md',
    defaultValue: 50,
    'aria-label': 'slider',
  },
  SliderFilledTrack: {},
  SliderThumb: {},
  SliderTrack: {},
```
Then to utils editor.ts
```ts
const SLIDER_COMPONENTS: (ComponentType | MetaComponentType)[] = [
  'Slider',
  'SliderTrack',
  'SliderFilledTrack',
  'SliderThumb',
]
  ///
   ...SLIDER_COMPONENTS,
   ///
    'SliderMeta',
    //
            'SliderTrack',
        'SliderFilledTrack',
        'SliderThumb',
```
