
[//]: # (Custom component imports)

import DocString from '@site/src/components/DocString';
import PythonCode from '@site/src/components/PythonCode';

import AppDisplay from '@site/src/components/AppDisplay';
import SectionBreak from '@site/src/components/SectionBreak';
import AppendixSection from '@site/src/components/AppendixSection';

[//]: # (Docstring)

import DocstringSource from '!!raw-loader!./a1-[autogen]/docstring.txt';
import PythonSource from '!!raw-loader!./a1-[autogen]/python_code.txt';


<DocString>{DocstringSource}</DocString>
<PythonCode GLink='SCIPY/signal/PERIODOGRAM/PERIODOGRAM.py'>{PythonSource}</PythonCode>


<SectionBreak />

    

[//]: # (Examples)

## Examples

<AppDisplay 
  GLink='SCIPY/signal/PERIODOGRAM'
  nodeLabel='PERIODOGRAM'>
</AppDisplay>

<SectionBreak />

    

[//]: # (Appendix)

import Notes from '!!raw-loader!./appendix/notes.md';
import Hardware from '!!raw-loader!./appendix/hardware.md';
import Media from '!!raw-loader!./appendix/media.md';

## Appendix

<AppendixSection index={0} folderPath='nodes/SCIPY/signal/PERIODOGRAM/appendix/'>{Notes}</AppendixSection>
<AppendixSection index={1} folderPath='nodes/SCIPY/signal/PERIODOGRAM/appendix/'>{Hardware}</AppendixSection>
<AppendixSection index={2} folderPath='nodes/SCIPY/signal/PERIODOGRAM/appendix/'>{Media}</AppendixSection>


