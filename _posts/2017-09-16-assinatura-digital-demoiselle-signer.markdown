---
layout: post
date: 2017-09-16T21:05:04-03:00
title: "Assinatura Digital - Demoiselle Signer"
author: cesaraugusto
abstract:  >
  Assinatura digital de documentos. Teoria, e prática. Framework para java - Demoiselle Signer
---

Recentemente foi desenvolvido um sistema de assinatura digital para dois clientes. Para a empresa EmedBr foram utilizados módulos prontos, fornecidos pela Certisign e pela Eval. Estes módulos requerem uma assinatura, com custo mensal para sua utilização.

Demonstrarei nesse Drop a solução que utilizamos para atender ao Tribunal de Justiça RS, pois se trata de um framework opensource, de fácil utilização, e bastante eficiente.

Vamos a um pouco de teoria.

### O que é assinatura digital
Quando precisamos garantir a integridade e a legitimidade de algum documento, o que precisamos fazer é nos dirigirmos a algum tabelionato, ou cartório, para que um tabelião, dotado de fé pública, verifique nossos documentos, e por meio de selos e carimbos “reconheça firma”.

No mundo digital, diversas são as necessidades que exigem também um reconhecimento de firma. Intrusões, violações de conformidade e ataques cibernéticos, ou a simples facilidade de manter um documento virtual – sem a necessidade de impressão, e o consequente problema de armazenamento, e a limitada distribuição – e mesmo assim com toda a comprovação que um arquivo físico pode ter.

A certificação digital funciona basicamente como uma “carteira de identidade eletrônica”, com validade jurídica e que garante a proteção e a identificação das partes envolvidas.

A utilização da assinatura ou firma digital providencia a prova inegável de que uma mensagem ou documento recebida pelo destinatário realmente foi originada no emissor. Para verificar este requisito, uma assinatura digital deve conter as seguintes propriedades:
    - autenticidade: o receptor deve poder confirmar que a assinatura foi feita pelo emissor;
    - integridade: qualquer alteração da mensagem faz com que a assinatura não corresponda mais ao documento;
    - irretratabilidade ou não-repúdio: o emissor não pode negar a autenticidade da mensagem.

### O que é um certificado digital
É um arquivo eletrônico que permite conhecer o titular da mensagem.

Um certificado digital é emitido por uma entidade certificadora, que aqui faz o papel do tabelião, e vinculada a um cpf, ou cnpj. O portador do eCpf ou eCnpj, recebe um arquivo (A1), um token, ou um cartão (A3), e uma senha, que irá compor a chave privada.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-16-assinatura-digital-demoiselle-signer/1.png" />
</center>
  
Existem outros tipos de certificado digital: Tipo S, para criptografar documentos, tipo T para certificar a data de assinatura, etc. Porém aqui no nosso caso, estamos tratando apenas dos certificados do tipo A, que garantem a assinatura digital.

### ICP Brasil
Desde 2001, existe no Brasil a medida provisória nº 2.200-2, que regulamenta a assinatura digital. A Infraestrutura de Chaves Públicas Brasileira (ICP Brasil) que é um conjunto de tecnologias (técnicas, práticas e procedimentos) que garante às transações e aos documentos eletrônicos a segurança por meio do uso de um par de chaves.

O que a MP portanto outorga à ICP-Brasil é a fé pública, considerando que qualquer documento digital assinado com o certificado emitido pela ICP-Brasil pode de fato ser considerado assinado pela própria pessoa.

Ou seja, qualquer sistema de assinatura digital precisa seguir esta regra para ter validade jurídica no país.

### Demoiselle Signer
Agora a parte boa, vamos à prática.

<center>
  <img style="margin: 10px" src="{{ site.baseurl }}/content/2017-09-16-assinatura-digital-demoiselle-signer/2.png" />
</center>

Dependencias Maven:

    <dependency>
        <groupId>org.demoiselle.signer</groupId>
        <artifactId>core</artifactId>
        <version>3.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.demoiselle.signer</groupId>
        <artifactId>policy-impl-cades</artifactId>
        <version>3.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.demoiselle.signer</groupId>
        <artifactId>chain-icp-brasil</artifactId>
        <version>3.1.2</version>
    </dependency>

Carregando o driver, e alguns tratamentos que precisamos fazer, para evitar erros do programa rodando, sem o token inserido e outra validações:

	private static KeyStore getKeystore() throws UIException {
		try{
			KeyStoreLoader loader = KeyStoreLoaderFactory.factoryKeyStoreLoader();
			loader.setCallbackHandler(new PinCallbackHandler());
			return loader.getKeyStore();
		}catch(DriverNotAvailableException e){
			logger.info("Drivers de certificados não foram encontrados: " + e.getMessage());
			throw new UIException("Não foi possível carregar os certificados. Certifique-se\n"
								+ "que o certificado está devidamente conectado e clique\n"
								+ "em Atualizar.");
		}catch (InvalidPinException e){
			logger.severe("Senha do certificado nula ou inválida:" + e.getMessage());
			throw new UIException("A senha informada é inválida, tente novamente.");	
		}catch(KeyStoreLoaderException e){
			logger.severe(e);
			logger.info("Não foi identificado um driver compatível com o hardware: " + e.getMessage());
			throw new UIException("Não foi encontrado um driver compatível com o do certificado.\n"
								+ "A instalação é necessária para o funcionamento do processo de \n"
								+ "assinatura.");	
		}catch(ProviderException e){
			logger.severe("Token removido:" + e.getMessage());
			throw new UIException("Verifique se o token/cartão está devidamente conectado.");
		}catch(Exception e){
			logger.severe(e);
			throw new UIException("Falha ao obter certificados: "+e.getMessage());
		}
	}

Listando os certificados instalados no pc. No nosso caso, filtramos os certificados que estejam vigentes, e do tipo A3. Isso vai depender de cada regra de negócio...

	public static List<String> getCertificados() throws UIException {
		keyStore = getKeystore();		
		try {				
			//Filtra por tipo
			List<String> aliases = new ArrayList<>();
			for (String alias : Collections.list(keyStore.aliases())){
				Certificate[] c = keyStore.getCertificateChain(alias);					
				if(keyStore.getKey(alias, null) == null){
					logger.info("O certificado de " + alias + " foi ignorado por não estar conectado.");
				}else{
					if(CertificateValidator.validar(c[0])){
						logger.info("O certificado de " + alias + " está disponível para exibição.");
						X509Certificate x509 = (X509Certificate)c[0];       
						BasicCertificate bCertificate = new BasicCertificate(x509);
						
						SimpleDateFormat dateFormater = new SimpleDateFormat("dd/MM/yyyy");					
						
						StringBuilder sb = new StringBuilder(bCertificate.getCertificateSubjectDN().get("CN").toString());
						sb.append(" - ");
						sb.append(dateFormater.format(bCertificate.getAfterDate()));
						aliases.add(sb.toString());
					}
				}
			}		
			logger.info(String.format("%d certificados encontrados.", aliases.size()));
			return aliases;
		} catch (Exception e) {
			logger.severe("Ao tentar obter certificados:" + e.getMessage());
			throw new UIException(FALHA_OBTER_DADOS_CERTIFICADO);			
		}
	}

Para listar os dados de um certificado:

	public static String getCertificateData(Certificado certificado) {
		try {	
			if (certificado != null){
				X509Certificate x509 = (X509Certificate)certificado.getCertificados()[0];       
				BasicCertificate bCertificate = new BasicCertificate(x509);
				
				StringBuilder sb = new StringBuilder();
				final String PATTERN = "%-18s\t\t: %s\n";
				final String PATTERN_B = "%-18s\t: %s\n";
				final String PATTERN_C = "%-18s: %s\n";
				
				SimpleDateFormat dtValidade = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss");
				
				sb.append(String.format(PATTERN, "Nome", bCertificate.getCertificateSubjectDN().get("CN")));
				sb.append(String.format(PATTERN, "E-mail", bCertificate.getEmail()));
				
				if (bCertificate.hasCertificatePF()) {
	                ICPBRCertificatePF ippBRCertificatePF = bCertificate.getICPBRCertificatePF();
				
	                String dtNascimento = ippBRCertificatePF.getDataNascimento();
	                String dtNascimentoFormatada = String.format("%s/%s/%s", dtNascimento.substring(0,2), dtNascimento.substring(2,4), dtNascimento.substring(4));
	                
					sb.append(String.format(PATTERN, "CPF", ippBRCertificatePF.getCPF()));
					sb.append(String.format(PATTERN, "RG", ippBRCertificatePF.getRg()));
					sb.append(String.format(PATTERN_C, "Data de nascimento", dtNascimentoFormatada ));
				
				}
				sb.append(String.format("%-18s\t\t: %s até %s\n", "Validade", dtValidade.format(bCertificate.getBeforeDate()), dtValidade.format(bCertificate.getAfterDate())));
				sb.append(String.format(PATTERN, "Emissor", bCertificate.getCertificateIssuerDN().get("O")));
				sb.append(String.format(PATTERN_B, "Tipo Certificado", bCertificate.getNivelCertificado()));				
				sb.append(String.format(PATTERN_B, "Número de série", bCertificate.getSerialNumber()));
				sb.append(String.format(PATTERN_B, "Uso da chave", bCertificate.getICPBRKeyUsage()));
				
				return sb.toString();
			}			
		} catch (IOException e) {
			logger.severe(e);
		}
		return "";
	}

Configurando a assinatura. Aqui tudo está conforme a necessidade que tínhamos. Alguns parâmetros podem variar de acordo com a necessidade, em todo caso, funciona assim:

    public static AssinadorManager preparaAssinador(final Certificado certificado, AssinadorConfiguracao configuracao){
    	PKCS7Signer assinador = PKCS7Factory.getInstance().factoryDefault();
    	assinador.setCertificates(certificado.getCertificados());
    	assinador.setPrivateKey((PrivateKey) certificado.getChave());
    	assinador.setSignaturePolicy(new ADRBCMS_2_2());
    	
    	boolean tipoUnico = configuracao.getTypeAssinatura().equals(TypeSignature.AGGREGATED);     	
    	assinador.setAttached(tipoUnico);
    	
		return new AssinadorManager(assinador);
    }

Depois é só assinar:

    public byte[] assinar(byte[] conteudo){
		return assinador.signer(conteudo);    	
    }

### Relacionados
- https://pt.wikipedia.org/wiki/Assinatura_digital
- http://blog.validcertificadora.com.br/?p=8378
- http://demoiselle.io/signer/index.html
- http://www.certisign.com.br
- https://www.evaltec.com.br
- https://cartorioonlinebrasil24h.com.br/blog/reconhecimento-de-firma-como-e-feito
