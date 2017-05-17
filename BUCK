from os.path import basename
from os.path import dirname

def merge_dicts(x, y):
  z = x.copy()
  z.update(y)
  return z;

def filename(x):
  return basename(x)[0];

genrule(
  name = 'cmake',
  out = 'out',
  srcs = glob([
    '*.in',
    '*.txt',
    '*.TXT',
    'cmake/**/*.cmake',
    'cmake/**/*.in',
    'cmake/**/*.guess',
    'cmake/**/*.cpp',
    'cmake/**/*.txt',
    'docs/**/*.in',
    'docs/**/*.rst',
    'docs/**/*.txt',
    'examples/**/*.cpp',
    'examples/**/*.txt',
    'include/**/*.cmake',
    'include/**/*.build',
    'include/**/*.h',
    'include/**/*.in',
    'include/**/*.txt',
    'lib/**/*.cpp',
    'lib/**/*.c',
    'lib/**/*.txt',
    'projects/**/*.cpp',
    'projects/**/*.txt',
    'runtimes/**/*.in',
    'runtimes/**/*.txt',
    'tools/**/*.c',
    'tools/**/*.cc',
    'tools/**/*.cpp',
    'tools/**/*.in',
    'tools/**/*.txt',
    'test/**/*',
    'unittests/**/*',
    'utils/**/*.h',
    'utils/**/*.cpp',
    'utils/**/*.cc',
    'utils/**/*.c',
    'utils/**/*.in',
    'utils/**/*.py',
    'utils/**/*.txt',
    'utils/**/llvm-build',
  ], excludes = glob([
    'examples/BrainF2/**/*',
    'examples/Fibonacci2/**/*',
  ])),
  cmd = 'mkdir -p $OUT && ' +
        'cd $OUT && ' +
        'cmake $SRCDIR',
)

genrule(
  name = 'config.h',
  out = 'config.h',
  cmd = 'cp $(location :cmake)/include/llvm/Config/config.h $OUT',
)

genrule(
  name = 'llvm-config.h',
  out = 'llvm-config.h',
  cmd = 'cp $(location :cmake)/include/llvm/Config/llvm-config.h $OUT',
)

genrule(
  name = 'abi-breaking.h',
  out = 'abi-breaking.h',
  cmd = 'cp $(location :cmake)/include/llvm/Config/abi-breaking.h $OUT',
)

genrule(
  name = 'Targets.def',
  out = 'Targets.def',
  cmd = 'cp $(location :cmake)/include/llvm/Config/Targets.def $OUT',
)

genrule(
  name = 'AsmParsers.def',
  out = 'AsmParsers.def',
  cmd = 'cp $(location :cmake)/include/llvm/Config/AsmParsers.def $OUT',
)

genrule(
  name = 'AsmPrinters.def',
  out = 'AsmPrinters.def',
  cmd = 'cp $(location :cmake)/include/llvm/Config/AsmPrinters.def $OUT',
)

genrule(
  name = 'Disassemblers.def',
  out = 'Disassemblers.def',
  cmd = 'cp $(location :cmake)/include/llvm/Config/Disassemblers.def $OUT',
)

prebuilt_cxx_library(
  name = 'config',
  header_only = True,
  header_namespace = 'llvm',
  exported_headers = merge_dicts(subdir_glob([
    ('include/llvm', 'Config/**/*.h'),
  ]), {
    'Config/config.h': ':config.h',
    'Config/llvm-config.h': ':llvm-config.h',
    'Config/abi-breaking.h': ':abi-breaking.h',
    'Config/Targets.def': ':Targets.def',
    'Config/AsmParsers.def': ':AsmParsers.def',
    'Config/AsmPrinters.def': ':AsmPrinters.def',
    'Config/Disassemblers.def': ':Disassemblers.def',
  }),
)

prebuilt_cxx_library(
  name = 'adt',
  header_only = True,
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'ADT/**/*.h'),
  ]),
)

prebuilt_cxx_library(
  name = 'llvm-c',
  header_only = True,
  header_namespace = 'llvm-c',
  exported_headers = subdir_glob([
    ('include/llvm-c', '**/*.h'),
  ]),
)

cxx_library(
  name = 'demangle',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Demangle/**/*.h'),
  ]),
  srcs = glob([
    'lib/Demangle/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
)

prebuilt_cxx_library(
  name = 'codegen-headers',
  header_only = True,
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'CodeGen/**/*.h'),
  ]),
)

genrule(
  name = 'DataTypes.h',
  out = 'DataTypes.h',
  cmd = 'cp $(location :cmake)/include/llvm/Support/DataTypes.h $OUT',
)

cxx_library(
  name = 'support-c',
  srcs = glob([
    'lib/Support/*.c',
  ]),
  deps = [
    ':config',
  ]
)

cxx_library(
  name = 'support',
  header_namespace = 'llvm',
  exported_headers = merge_dicts(subdir_glob([
    ('include/llvm', 'Support/**/*.h'),
    ('include/llvm', 'Support/**/*.inc'),
    ('include/llvm', 'Support/**/*.def'),
  ]), {
    'Support/DataTypes.h': ':DataTypes.h',
  }),
  srcs = glob([
    'lib/Support/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  exported_linker_flags = [
    '-lcurses',
    '-lz',
  ],
  deps = [
    ':config',
    ':adt',
    ':llvm-c',
    ':codegen-headers',
    ':demangle',
    ':support-c',
  ],
)

cxx_library(
  name = 'codegen',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'CodeGen/**/*.h'),
    ('include/llvm', 'CodeGen/**/*.def'),
  ]),
  srcs = glob([
    'lib/CodeGen/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':analysis',
    ':support',
    ':config',
    ':ir',
    ':target',
    ':transforms',
  ],
)

cxx_library(
  name = 'passes',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Pass.h'),
    ('include/llvm', 'InitializePasses.h'),
    ('include/llvm', 'Pass*.h'),
    ('include/llvm', 'Passes/**/*.h'),
  ]),
  headers = subdir_glob([
    ('include/llvm', 'Analysis/**/*.h'),
    ('include/llvm', 'Analysis/**/*.def'),
    ('include/llvm', 'Target/*.h'),
    ('include/llvm', 'Transforms/**/*.h'),
    ('include/llvm', 'ProfileData/**/*.h'),
    ('include/llvm', 'ProfileData/**/*.inc'),
  ]),
  srcs = glob([
    'lib/Passes/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':mc',
  ],
)

genrule(
  name = 'Attributes.gen',
  out = 'Attributes.gen',
  srcs = glob([
    'include/llvm/IR/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-attrs -I $SRCDIR/include $SRCDIR/include/llvm/IR/Attributes.td -o $OUT',
)

genrule(
  name = 'Intrinsics.gen',
  out = 'Intrinsics.gen',
  srcs = glob([
    'include/llvm/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-intrinsic -I $SRCDIR/include $SRCDIR/include/llvm/IR/Intrinsics.td -o $OUT',
)

genrule(
  name = 'AttributesCompatFunc.inc',
  out = 'AttributesCompatFunc.inc',
  srcs = glob([
    'include/llvm/IR/**/*.gen',
    'include/llvm/IR/**/*.td',
    'lib/IR/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-attrs -I $SRCDIR/include $SRCDIR/lib/IR/AttributesCompatFunc.td -o $OUT',
)

cxx_library(
  name = 'ir',
  header_namespace = '',
  exported_headers = merge_dicts(subdir_glob([
    ('include', 'llvm/IR/**/*.h'),
    ('include', 'llvm/IR/**/*.def'),
    ('include', 'llvm/IR/**/*.gen'),
  ]), {
    'llvm/IR/Attributes.gen': ':Attributes.gen',
    'llvm/IR/Intrinsics.gen': ':Intrinsics.gen',
  }),
  headers = merge_dicts(subdir_glob([
    ('include', 'llvm/Pass.h'),
    ('include', 'llvm/PassInfo.h'),
    ('include', 'llvm/PassSupport.h'),
    ('include', 'llvm/PassRegistry.h'),
    ('include', 'llvm/PassAnalysisSupport.h'),
    ('include', 'llvm/InitializePasses.h'),
    ('include', 'llvm/Analysis/**/*.h'),
    ('include', 'llvm/Bitcode/**/*.h'),
  ]), {
    'AttributesCompatFunc.inc': ':AttributesCompatFunc.inc',
  }),
  srcs = glob([
    'lib/IR/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
  ],
)

cxx_library(
  name = 'analysis',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Analysis/**/*.h'),
    ('include/llvm', 'Analysis/**/*.def'),
  ]),
  headers = subdir_glob([
    ('include/llvm', 'ProfileData/**/*.h'),
    ('include/llvm', 'ProfileData/**/*.inc'),
    ('include/llvm', 'Transforms/**/*.h'),
    ('include/llvm', 'Object/**/*.h'),
  ]),
  srcs = glob([
    'lib/Analysis/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':ir',
    ':passes',
  ],
)

cxx_library(
  name = 'bitcode',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Bitcode/**/*.h'),
  ]),
  srcs = glob([
    'lib/Bitcode/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':analysis',
    ':passes',
  ],
)

cxx_library(
  name = 'object',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Object/**/*.h'),
  ]),
  srcs = glob([
    'lib/Object/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':mc',
    ':ir',
    ':analysis',
    ':bitcode',
  ],
)

cxx_library(
  name = 'profiledata',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'ProfileData/**/*.h'),
    ('include/llvm', 'ProfileData/**/*.inc'),
  ]),
  srcs = glob([
    'lib/ProfileData/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':object',
  ],
)

cxx_library(
  name = 'asmparser',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'AsmParser/**/*.h'),
  ]),
  srcs = glob([
    'lib/AsmParser/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':passes',
  ],
)

cxx_library(
  name = 'irreader',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'IRReader/**/*.h'),
  ]),
  srcs = glob([
    'lib/IRReader/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':llvm-c',
    ':support',
    ':asmparser',
    ':bitcode',
  ],
)

cxx_library(
  name = 'linker',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Linker/**/*.h'),
  ]),
  headers = subdir_glob([
    ('include/llvm', 'Transforms/**/*.h'),
  ]),
  srcs = glob([
    'lib/Linker/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':analysis',
    ':passes',
  ],
)

cxx_library(
  name = 'transforms',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Transforms/**/*.h'),
  ]),
  srcs = glob([
    'lib/Transforms/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':ir',
    ':analysis',
    ':passes',
    ':profiledata',
    ':bitcode',
    ':irreader',
    ':linker',
    ':target',
  ],
)

debuginfo_windows_sources = glob([
  'lib/DebugInfo/PDB/**/*.cpp',
])

debuginfo_platform_sources = debuginfo_windows_sources

cxx_library(
  name = 'debuginfo',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'DebugInfo/**/*.h'),
    ('include/llvm', 'DebugInfo/**/*.def'),
  ]),
  headers = subdir_glob([
    ('include/llvm', 'Object/**/*.h'),
    ('include/llvm', 'MC/**/*.h'),
  ]),
  srcs = glob([
    'lib/DebugInfo/**/*.cpp',
  ], excludes = debuginfo_platform_sources),
  platform_srcs = [
    ('^windows.*', debuginfo_windows_sources),
  ],
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':config',
    ':support',
  ],
)

cxx_library(
  name = 'mc',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'MC/**/*.h'),
  ]),
  srcs = glob([
    'lib/MC/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':debuginfo',
  ],
  visibility = [
    '//...',
  ],
)

cxx_library(
  name = 'tablegen',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'TableGen/**/*.h'),
  ]),
  srcs = glob([
    'lib/TableGen/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
  ],
)

cxx_library(
  name = 'executionengine',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'ExecutionEngine/**/*.h'),
  ]),
  srcs = glob([
    'lib/ExecutionEngine/**/*.cpp',
  ], excludes = glob([
    'lib/ExecutionEngine/IntelJITEvents/**/*.cpp',
    'lib/ExecutionEngine/OProfileJIT/**/*.cpp',
  ])),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':config',
    ':ir',
    ':support',
    ':debuginfo',
    ':transforms',
  ],
)

cxx_library(
  name = 'target',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Target/**/*.h'),
    ('include/llvm', 'Target/**/*.def'),
  ]),
  srcs = glob([
    'lib/Target/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':mc',
    ':analysis',
    ':passes',
  ],
)

cxx_library(
  name = 'lto',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'LTO/**/*.h'),
  ]),
  srcs = glob([
    'lib/LTO/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
    ':ir',
    ':passes',
    ':analysis',
    ':target',
    ':linker',
    ':object',
    ':transforms',
    ':executionengine',
  ],
)

cxx_library(
  name = 'xray',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'XRay/**/*.h'),
  ]),
  srcs = glob([
    'lib/XRay/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':support',
  ],
)

cxx_binary(
  name = 'llvm-tblgen',
  header_namespace = '',
  headers = subdir_glob([
    ('utils/TableGen', '**/*.h'),
    ('include', 'llvm/Target/**/*.h'),
    ('include', 'llvm/Target/**/*.def'),
    ('include', 'llvm/TableGen/**/*.h'),
    ('include', 'llvm/MC/**/*.h'),
  ]),
  srcs = glob([
    'lib/TableGen/**/*.cpp',
    'utils/TableGen/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  visibility = [
    '//...',
  ],
  deps = [
    ':adt',
    ':support',
  ],
)

genrule(
  name = 'WebAssemblyGenRegisterInfo.inc',
  out = 'WebAssemblyGenRegisterInfo.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-register-info -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenInstrInfo.inc',
  out = 'WebAssemblyGenInstrInfo.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-instr-info -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenSubtargetInfo.inc',
  out = 'WebAssemblyGenSubtargetInfo.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-subtarget -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenMCCodeEmitter.inc',
  out = 'WebAssemblyGenMCCodeEmitter.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-emitter -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenAsmWriter.inc',
  out = 'WebAssemblyGenAsmWriter.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-asm-writer -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenDAGISel.inc',
  out = 'WebAssemblyGenDAGISel.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-dag-isel -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

genrule(
  name = 'WebAssemblyGenFastISel.inc',
  out = 'WebAssemblyGenFastISel.inc',
  srcs = glob([
    'include/llvm/**/*.td',
    'lib/Target/WebAssembly/**/*.td',
  ]),
  cmd = '$(location :llvm-tblgen) -gen-fast-isel -I $SRCDIR/include -I $SRCDIR/lib/Target/WebAssembly $SRCDIR/lib/Target/WebAssembly/WebAssembly.td -o $OUT',
)

cxx_library(
  name = 'webassembly',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/WebAssembly', '**/*.h'),
  ]),
  headers = {
    'WebAssemblyGenRegisterInfo.inc': ':WebAssemblyGenRegisterInfo.inc',
    'WebAssemblyGenInstrInfo.inc': ':WebAssemblyGenInstrInfo.inc',
    'WebAssemblyGenSubtargetInfo.inc': ':WebAssemblyGenSubtargetInfo.inc',
    'WebAssemblyGenMCCodeEmitter.inc': ':WebAssemblyGenMCCodeEmitter.inc',
    'WebAssemblyGenAsmWriter.inc': ':WebAssemblyGenAsmWriter.inc',
    'WebAssemblyGenDAGISel.inc': ':WebAssemblyGenDAGISel.inc',
    'WebAssemblyGenFastISel.inc': ':WebAssemblyGenFastISel.inc',
  },
  srcs = glob([
    'lib/Target/WebAssembly/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

def tablegen(x, n, t):
  parent = dirname(x)
  genrule(
    name = n,
    out = n,
    srcs = glob([
      'include/llvm/**/*.td',
      parent + '/**/*.td',
    ]),
    cmd = '$(location :llvm-tblgen) ' + t + ' -I $SRCDIR/include -I $SRCDIR/' + parent + ' $SRCDIR/' + x + ' -o $OUT',
  )
  prebuilt_cxx_library(
    name = 'lib' + n,
    header_namespace = '',
    header_only = True,
    exported_headers = {
      n: ':' + n,
    },
  )
  return ':lib' + n

cxx_library(
  name = 'x86',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/X86', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/X86/**/*.cpp',
  ], excludes = [
    'lib/Target/X86/X86CallLowering.cpp',
    # 'lib/Target/X86/InstPrinter/X86IntelInstPrinter.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/X86/X86.td', 'X86GenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/X86/X86.td', 'X86GenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/X86/X86.td', 'X86GenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/X86/X86.td', 'X86GenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/X86/X86.td', 'X86GenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/X86/X86.td', 'X86GenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/X86/X86.td', 'X86GenAsmWriter1.inc', '-gen-asm-writer -asmwriternum=1'),
    tablegen('lib/Target/X86/X86.td', 'X86GenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/X86/X86.td', 'X86GenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/X86/X86.td', 'X86GenAsmMatcher.inc', '-gen-asm-matcher'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

prebuilt_cxx_library(
  name = 'llvm',
  header_only = True,
  exported_deps = [
    # Modules
    ':adt',
    ':analysis',
    ':asmparser',
    ':bitcode',
    ':codegen',
    ':config',
    ':executionengine',
    ':ir',
    ':irreader',
    ':linker',
    ':lto',
    ':mc',
    ':passes',
    ':profiledata',
    ':support',
    ':target',
    ':xray',
    # Targets
    ':webassembly',
    ':x86',
  ],
  visibility = [
    'PUBLIC',
  ],
)
