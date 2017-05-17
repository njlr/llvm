from os.path import basename

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

# prebuilt_cxx_library(
#   name = 'llvm-headers',
#   header_only = True,
#   header_namespace = 'llvm',
#   exported_headers = merge_dicts(subdir_glob([
#     ('include/llvm', '**/*.h'),
#     ('include/llvm', '**/*.def'),
#   ]), {
#     'Config/config.h': ':config.h',
#     'Config/llvm-config.h': ':llvm-config.h',
#     'Config/abi-breaking.h': ':abi-breaking.h',
#   }),
# )

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
  deps = [
  ],
)

# targets = [
#   'AArch64',
#   'AMDGPU',
#   'ARM',
#   'AVR',
#   'BPF',
#   'Hexagon',
#   'Lanai',
#   'MSP430',
#   'Mips',
#   'NVPTX',
#   # 'PowerPC',
#   'RISCV',
#   'Sparc',
#   'SystemZ',
#   'WebAssembly',
#   'X86',
#   'XCore',
# ]

def generate_intrinsics(target):
  genrule(
    name = target + 'GenIntrinsics.inc',
    out = target + 'GenIntrinsics.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-intrinsic -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenIntrinsics.inc'

def generate_system_operands(target):
  genrule(
    name = target + 'GenSystemOperands.inc',
    out = target + 'GenSystemOperands.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-searchable-tables -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenSystemOperands.inc'

def generate_asm_writer(target):
  genrule(
    name = target + 'GenAsmWriter.inc',
    out = target + 'GenAsmWriter.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-asm-writer -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenAsmWriter.inc'

def generate_register_info(target):
  return "aah"

def generate_intrinsic_info(target):
  genrule(
    name = target + 'GenInstrInfo.inc',
    out = target + 'GenInstrInfo.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-instr-info -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenInstrInfo.inc'

def generate_disassembler_tables(target):
  genrule(
    name = target + 'GenDisassemblerTables.inc',
    out = target + 'GenDisassemblerTables.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-disassembler -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenInstrInfo.inc'

def generate_subtarget_info(target):
  genrule(
    name = target + 'GenSubtargetInfo.inc',
    out = target + 'GenSubtargetInfo.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-subtarget -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenInstrInfo.inc'

def generate_callingconv(target):
  genrule(
    name = target + 'GenCallingConv.inc',
    out = target + 'GenCallingConv.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-callingconv -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenCallingConv.inc'

def generate_pseudo_lowering(target):
  genrule(
    name = target + 'GenMCPseudoLowering.inc',
    out = target + 'GenMCPseudoLowering.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-pseudo-lowering -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenMCPseudoLowering.inc'

def generate_dag_isel(target):
  genrule(
    name = target + 'GenDAGISel.inc',
    out = target + 'GenDAGISel.inc',
    srcs = glob([
      'include/llvm/**/*.td',
      'lib/Target/' + target + '/*.td',
    ]),
    cmd = 'llvm-tblgen -gen-dag-isel -I $SRCDIR/include -I $SRCDIR/lib/target/' + target + ' $SRCDIR/lib/Target/' + target + '/' + target + '.td -o $OUT',
  )
  return ':' + target + 'GenDAGISel.inc'

# windows_sources = glob([
#   'lib/DebugInfo/PDB/**/*.cpp',
# ])
#
# platform_sources = windows_sources

# cxx_library(
#   name = 'llvm',
#   header_namespace = '',
#   exported_headers = merge_dicts(subdir_glob([
#     ('include', '**/*.h'),
#     ('include', '**/*.inc'),
#     ('include', '**/*.def'),
#     # ('include', '**/*.gen'),
#   ]), {
#     'llvm/Config/config.h': ':config.h',
#     'llvm/Config/llvm-config.h': ':llvm-config.h',
#     'llvm/Config/abi-breaking.h': ':abi-breaking.h',
#     'llvm/Config/Targets.def': ':Targets.def',
#     'llvm/Config/AsmParsers.def': ':AsmParsers.def',
#     'llvm/Config/AsmPrinters.def': ':AsmPrinters.def',
#     'llvm/Config/Disassemblers.def': ':Disassemblers.def',
#     'llvm/Support/DataTypes.h': ':DataTypes.h',
#     'llvm/IR/Attributes.gen': ':Attributes.gen',
#     'llvm/IR/Intrinsics.gen': ':Intrinsics.gen',
#   }),
#   headers = merge_dicts(subdir_glob([
#     ('lib/Fuzzer', '**/*.h'),
#     ('lib/Target/AArch64', '**/*.h'),
#     ('lib/Target/AMDGPU', '**/*.h'),
#     ('lib/Target/ARM', '**/*.h'),
#     ('lib/Target/AVR', '**/*.h'),
#     ('lib/Target/BPF', '**/*.h'),
#     ('lib/Target/Hexagon', '**/*.h'),
#     ('lib/Target/Lanai', '**/*.h'),
#     ('lib/Target/Mips', '**/*.h'),
#     ('lib/Target/MSP430', '**/*.h'),
#     ('lib/Target/NVPTX', '**/*.h'),
#     ('lib/Target/PowerPC', '**/*.h'),
#     ('lib/Target/RISCV', '**/*.h'),
#     ('lib/Target/Sparc', '**/*.h'),
#     ('lib/Target/SystemZ', '**/*.h'),
#     ('lib/Target/WebAssembly', '**/*.h'),
#     ('lib/Target/X86', '**/*.h'),
#     ('lib/Target/XCore', '**/*.h'),
#   ]),
#   dict(
#     # [ (x + 'GenIntrinsics.inc',         generate_intrinsics(x))          for x in targets ] +
#     # [ (x + 'GenAsmWriter.inc',          generate_asm_writer(x))          for x in targets ] +
#     # [ (x + 'GenRegisterInfo.inc',       generate_register_info(x))       for x in targets ] +
#     # [ (x + 'GenInstrInfo.inc',          generate_intrinsic_info(x))      for x in targets ] +
#     # [ (x + 'GenDisassemblerTables.inc', generate_disassembler_tables(x)) for x in targets ] +
#     # [ (x + 'GenSubtargetInfo.inc',      generate_subtarget_info(x))      for x in targets ] +
#     # [ (x + 'GenCallingConv.inc',        generate_callingconv(x))         for x in targets ] +
#     # [ (x + 'GenSystemOperands.inc',     generate_system_operands(x))     for x in targets ] +
#     # [ (x + 'GenDAGISel.inc',            generate_dag_isel(x))            for x in targets ] +
#     # [ (x + 'GenMCPseudoLowering.inc',   generate_pseudo_lowering(x))     for x in targets ]
#   )),
#   srcs = glob([
#     'lib/**/*.cpp',
#     # 'lib/Analysis/**/*.cpp',
#     # 'lib/Demangle/**/*.cpp',
#     # 'lib/Support/**/*.cpp',
#     # 'lib/Support/**/*.c',
#     # 'lib/TableGen/**/*.cpp',
#   ], excludes = platform_sources + [
#     'lib/Target/ARM/ARMLegalizerInfo.cpp',
#     'lib/Target/AArch64/AArch64RegisterBankInfo.cpp',
#   ]),
#   platform_srcs = [
#     ('^windows.*', windows_sources),
#   ],
#   compiler_flags = [
#     '-std=c++14',
#   ],
#   exported_linker_flags = [
#     '-lcurses',
#   ],
#   visibility = [
#     'PUBLIC',
#   ],
# )

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
    ('include/llvm', '**/*.h'),
    ('include/llvm', '**/*.def'),
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

cxx_library(
  name = 'ir',
  header_namespace = 'llvm',
  exported_headers = merge_dicts(subdir_glob([
    ('include/llvm', 'Pass.h'),
    ('include/llvm', 'PassInfo.h'),
    ('include/llvm', 'PassSupport.h'),
    ('include/llvm', 'PassRegistry.h'),
    ('include/llvm', 'PassAnalysisSupport.h'),
    ('include/llvm', 'InitializePasses.h'),
    ('include/llvm', 'IR/**/*.h'),
    ('include/llvm', 'IR/**/*.def'),
    ('include/llvm', 'IR/**/*.gen'),
  ]), {
    'IR/Attributes.gen': ':Attributes.gen',
    'IR/Intrinsics.gen': ':Intrinsics.gen',
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
  srcs = glob([
    'lib/Analysis/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':ir',
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
    # ':mc',
    ':ir',
    # ':analysis',
    # ':passes',
    # ':bitcode',
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
  srcs = glob([
    'lib/Linker/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    # ':llvm-c',
    # ':support',
    # ':asmparser',
    # ':bitcode',
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

cxx_library(
  name = 'debuginfo',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'DebugInfo/**/*.h'),
    ('include/llvm', 'DebugInfo/**/*.def'),
  ]),
  headers = subdir_glob([
    ('include/llvm', 'Object/**/*.h'),
  ]),
  srcs = glob([
    'lib/DebugInfo/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':adt',
    ':config',
    ':support',
    # ':object',
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
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    # ':adt',
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

prebuilt_cxx_library(
  name = 'llvm',
  header_only = True,
  exported_deps = [
    ':config',
    ':adt',
    ':support',
    ':executionengine',
    ':analysis',
    ':bitcode',
    ':passes',
    ':ir',
    ':mc',
    ':target',
    ':webassembly',
  ],
  visibility = [
    'PUBLIC',
  ],
)
