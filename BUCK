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
    'examples2/**/*',
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
  exported_platform_linker_flags = [
    ('default', ['-ldl', '-pthread']),
    ('linux.*', ['-ldl', '-pthread']),
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
  name = 'objectyaml',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'ObjectYAML/**/*.h'),
  ]),
  srcs = glob([
    'lib/ObjectYAML/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    ':support',
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
  name = 'option',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Option/**/*.h'),
  ]),
  srcs = glob([
    'lib/Option/**/*.cpp',
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
  name = 'libdriver',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'LibDriver/**/*.h'),
  ]),
  srcs = glob([
    'lib/LibDriver/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/LibDriver/Options.td', 'Options.inc', '-gen-opt-parser-defs'),
    ':adt',
    ':support',
    ':object',
    ':option',
  ],
)

cxx_library(
  name = 'lineeditor',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'LineEditor/**/*.h'),
  ]),
  srcs = glob([
    'lib/LineEditor/**/*.cpp',
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
  name = 'fuzzer',
  header_namespace = 'llvm',
  exported_headers = subdir_glob([
    ('include/llvm', 'Fuzzer/**/*.h'),
  ]),
  srcs = glob([
    'lib/Fuzzer/**/*.cpp',
  ], excludes = glob([
    'lib/Fuzzer/FuzzerTracePC.cpp',
    'lib/Fuzzer/test/**/*.cpp',
  ])),
  compiler_flags = [
    '-std=c++14',
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

cxx_library(
  name = 'aarch64',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/AArch64', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/AArch64/**/*.cpp',
  ], excludes = [
    'lib/Target/AArch64/AArch64CallLowering.cpp',
    'lib/Target/AArch64/AArch64InstructionSelector.cpp',
    'lib/Target/AArch64/AArch64LegalizerInfo.cpp',
    'lib/Target/AArch64/AArch64RegisterBankInfo.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenSystemOperands.inc', '-gen-searchable-tables'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenAsmWriter1.inc', '-gen-asm-writer  -asmwriternum=1'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/AArch64/AArch64.td', 'AArch64GenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'amdgpu',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/AMDGPU', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/AMDGPU/**/*.cpp',
  ], excludes = [
    'lib/Target/AMDGPU/AMDGPUCallLowering.cpp',
    'lib/Target/AMDGPU/SIISelLowering.cpp',
    'lib/Target/AMDGPU/AMDGPUISelLowering.cpp',
    'lib/Target/AMDGPU/AMDGPUCodeGenPrepare.cpp',
    'lib/Target/AMDGPU/R600ISelLowering.cpp',
    'lib/Target/AMDGPU/AMDGPUTargetTransformInfo.cpp',
    'lib/Target/AMDGPU/AMDGPUIntrinsicInfo.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenIntrinsics.inc', '-gen-intrinsic'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    tablegen('lib/Target/AMDGPU/AMDGPU.td', 'AMDGPUGenDFAPacketizer.inc', '-gen-dfa-packetizer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'arm',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/ARM', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/ARM/**/*.cpp',
  ], excludes = [
    'lib/Target/ARM/ARMLegalizerInfo.cpp',
    'lib/Target/ARM/ARMRegisterBankInfo.cpp',
    'lib/Target/ARM/ARMCallLowering.cpp',
    'lib/Target/ARM/ARMInstructionSelector.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/ARM/ARM.td', 'ARMGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'avr',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/AVR', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/AVR/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/AVR/AVR.td', 'AVRGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'bpf',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/BPF', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/BPF/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/BPF/BPF.td', 'BPFGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'hexagon',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/Hexagon', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/Hexagon/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    tablegen('lib/Target/Hexagon/Hexagon.td', 'HexagonGenDFAPacketizer.inc', '-gen-dfa-packetizer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'lanai',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/Lanai', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/Lanai/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    tablegen('lib/Target/Lanai/Lanai.td', 'LanaiGenDFAPacketizer.inc', '-gen-dfa-packetizer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'mips',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/Mips', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/Mips/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    tablegen('lib/Target/Mips/Mips.td', 'MipsGenDFAPacketizer.inc', '-gen-dfa-packetizer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'msp430',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/MSP430', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/MSP430/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenMCPseudoLowering.inc', '-gen-pseudo-lowering'),
    tablegen('lib/Target/MSP430/MSP430.td', 'MSP430GenDFAPacketizer.inc', '-gen-dfa-packetizer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'nvptx',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/NVPTX', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/NVPTX/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/NVPTX/NVPTX.td', 'NVPTXGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/NVPTX/NVPTX.td', 'NVPTXGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/NVPTX/NVPTX.td', 'NVPTXGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/NVPTX/NVPTX.td', 'NVPTXGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/NVPTX/NVPTX.td', 'NVPTXGenAsmWriter.inc', '-gen-asm-writer'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'powerpc',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/PowerPC', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/PowerPC/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/PowerPC/PPC.td', 'PPCGenSubtargetInfo.inc', '-gen-subtarget'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'riscv',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/RISCV', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/RISCV/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/RISCV/RISCV.td', 'RISCVGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/RISCV/RISCV.td', 'RISCVGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/RISCV/RISCV.td', 'RISCVGenInstrInfo.inc', '-gen-instr-info'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'sparc',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/Sparc', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/Sparc/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/Sparc/Sparc.td', 'SparcGenSubtargetInfo.inc', '-gen-subtarget'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'systemz',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/SystemZ', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/SystemZ/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenAsmMatcher.inc', '-gen-asm-matcher'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenDisassemblerTables.inc', '-gen-disassembler'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenMCCodeEmitter.inc', '-gen-emitter'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/SystemZ/SystemZ.td', 'SystemZGenSubtargetInfo.inc', '-gen-subtarget'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

cxx_library(
  name = 'webassembly',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/WebAssembly', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/WebAssembly/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/WebAssembly/WebAssembly.td', 'WebAssemblyGenMCCodeEmitter.inc', '-gen-emitter'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

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

cxx_library(
  name = 'xcore',
  header_namespace = '',
  exported_headers = subdir_glob([
    ('lib/Target/XCore', '**/*.h'),
  ]),
  srcs = glob([
    'lib/Target/XCore/**/*.cpp',
  ]),
  compiler_flags = [
    '-std=c++14',
  ],
  deps = [
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenRegisterInfo.inc', '-gen-register-info'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenInstrInfo.inc', '-gen-instr-info'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenSubtargetInfo.inc', '-gen-subtarget'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenDAGISel.inc', '-gen-dag-isel'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenFastISel.inc', '-gen-fast-isel'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenAsmWriter.inc', '-gen-asm-writer'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenCallingConv.inc', '-gen-callingconv'),
    tablegen('lib/Target/XCore/XCore.td', 'XCoreGenDisassemblerTables.inc', '-gen-disassembler'),
    ':adt',
    ':passes',
    ':mc',
    ':target',
    ':ir',
    ':transforms',
  ],
)

# This is a convenience library so that consumers
# don't have to depend on multiple modules.
prebuilt_cxx_library(
  name = 'llvm',
  header_namespace = '',
  header_only = True,
  exported_headers = subdir_glob([
    ('include', '**/*.h'),
  ]),
  exported_deps = [
    ':config',
  ],
  deps = [
    # Modules
    ':adt',
    ':analysis',
    ':asmparser',
    ':bitcode',
    ':codegen',
    ':debuginfo',
    ':demangle',
    ':executionengine',
    ':fuzzer',
    ':ir',
    ':irreader',
    ':libdriver',
    ':lineeditor',
    ':linker',
    ':lto',
    ':mc',
    ':object',
    ':objectyaml',
    ':option',
    ':passes',
    ':profiledata',
    ':support',
    ':tablegen',
    ':target',
    ':transforms',
    ':xray',
    # Targets
    ':aarch64',
    ':amdgpu',
    ':arm',
    ':avr',
    ':bpf',
    ':hexagon',
    ':lanai',
    ':mips',
    ':msp430',
    ':nvptx',
    ':powerpc',
    ':riscv',
    ':sparc',
    ':systemz',
    ':webassembly',
    ':x86',
    ':xcore',
  ],
  visibility = [
    'PUBLIC',
  ],
)
