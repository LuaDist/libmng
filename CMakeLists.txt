
	PROJECT(libmng)
	CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
	INCLUDE(dist.cmake)

###########################################################################################


# find libs
	FIND_PACKAGE( ZLIB REQUIRED)
	FIND_PACKAGE( JPEG REQUIRED) 
	
	FIND_PATH( LCMS_INCLUDE_DIR NAMES lcms.h )
	FIND_LIBRARY( LCMS_LIBRARY NAMES lcms)

	INCLUDE_DIRECTORIES( ${ZLIB_INCLUDE_DIR} ${JPEG_INCLUDE_DIR} ${LCMS_INCLUDE_DIR})

# libmng Library
	SET(LIBMNG_SRCS
		libmng_callback_xs.c
		libmng_chunk_io.c
		libmng_chunk_descr.c
		libmng_chunk_prc.c
		libmng_chunk_xs.c
		libmng_cms.c
		libmng_display.c
		libmng_dither.c
		libmng_error.c
		libmng_filter.c
		libmng_hlapi.c
		libmng_jpeg.c
		libmng_object_prc.c
		libmng_pixels.c
		libmng_prop_xs.c
		libmng_read.c
		libmng_trace.c
		libmng_write.c
		libmng_zlib.c
	)

		ADD_DEFINITIONS(-DMNG_ACCESS_CHUNKS )
		ADD_DEFINITIONS(-DMNG_STORE_CHUNKS)
		
		ADD_DEFINITIONS(-DMNG_FULL_CMS)
		ADD_DEFINITIONS(-DMNG_SUPPORT_FULL)
		

		ADD_DEFINITIONS(-DMNG_SUPPORT_TRACE -DMNG_ERROR_TELLTALE)

		ADD_DEFINITIONS(-DMNG_SUPPORT_READ -DMNG_SUPPORT_WRITE -DMNG_SUPPORT_DISPLAY)
		
	IF(MINGW)
		ADD_DEFINITIONS(-DMNG_BUILD_DLL )

		SET(M_LIBRARY m)
	ELSEIF(LINUX)
		ADD_DEFINITIONS(-DMNG_BUILD_SO )

		SET(M_LIBRARY m)
	ENDIF()

	ADD_LIBRARY(mng SHARED ${LIBMNG_SRCS})
	TARGET_LINK_LIBRARIES (mng ${ZLIB_LIBRARY}  ${JPEG_LIBRARY} ${LCMS_LIBRARY} ${M_LIBRARY})

	IF(MINGW)
		SET_TARGET_PROPERTIES(mng PROPERTIES
				LINK_FLAGS "-Wl,--add-stdcall-alias -Wl,--kill-at"
				CLEAN_DIRECT_OUTPUT 1)
	ENDIF()
	
# install	
	INSTALL(TARGETS mng RUNTIME DESTINATION ${INSTALL_BIN} LIBRARY DESTINATION ${INSTALL_LIB} ARCHIVE DESTINATION ${INSTALL_LIB})

	INSTALL(FILES libmng.h libmng_conf.h libmng_types.h DESTINATION ${INSTALL_INC})

	INSTALL(FILES CHANGES LICENSE README DESTINATION ${INSTALL_DOC})



