# Data files.

FILE(GLOB data *.xml )

message(STATUS "Data files ${data}")

install(FILES ${data} DESTINATION ${product}/${version}/genie)


# ExtGen (ubsim/ExtGen) total cross section splines and tables, one
# directory per external generator. Found by GENIEGen through the XSecTable
# fcl parameter, e.g. "genie/gibuu/<file>.xml" (uboonedata is on FW_SEARCH_PATH).
# The generator tarballs (*.tar.gz) are found the same way by the ExtGen grid init
# scripts (--gibuu-tarball genie/gibuu/<file>.tar.gz, --nuwro-tarball genie/nuwro/<file>.tar.gz).
FILE(GLOB gibuu_data gibuu/*.xml gibuu/*.txt gibuu/*.tar.gz )
if( gibuu_data )
  install(FILES ${gibuu_data} DESTINATION ${product}/${version}/genie/gibuu)
endif()
FILE(GLOB nuwro_data nuwro/*.xml nuwro/*.txt nuwro/*.tar.gz )
if( nuwro_data )
  install(FILES ${nuwro_data} DESTINATION ${product}/${version}/genie/nuwro)
endif()
