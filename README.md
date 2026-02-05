# DataTransferObject
DataTransferObject allows creating DTOs on the fly from request data based on a predefined parameter list. 
Properties that are not declared in the DTO are automatically skipped. Nested DTOs and DTO collections are supported.


### Example

```php
<?php

namespace App\Dto\AgentDTO\Document;

use App\Dto\AgentDTO\AgentBankDetailsDTO;
use App\Dto\DataTransferObject;
use Illuminate\Support\Str;

/**
 * @property int                     $user_id          Идентификатор пользователя (агента)
 * @property int|null                $document_number  Номер документа
 * @property string|null             $file_name        Имя файла документа
 * @property string|null             $document_type    Тип документа
 * @property string                  $directory_path  Путь для хранения документа
 * @property string|null             $uuid             UUID документа
 * @property AgentBankDetailsDTO|null $bank_details    Банковские реквизиты агента
 */
class AgentAgreementDTO extends DataTransferObject
{
    public function __construct(
        public int $user_id,
        public ?int $document_number,
        public ?string $file_name,
        public ?string $document_type,
        public string $directory_path,
        public ?string $uuid,
        public ?AgentBankDetailsDTO $bank_details,
    )
    {
    }

    /**
     * @inheritDoc
     * @return class-string[]
     */
    protected static function dependedDTO(): array
    {
        return ['bank_details' => AgentBankDetailsDTO::class];
    }

    /**
     * @inheritDoc
     * @return class-string[]
     */
    protected static function dependedCollectionDTO(): array
    {
        return [];
    }

    /**
     * @return array
     */
    protected static function getDefaultValues(): array
    {
        return [
            'directory_path' => 'private/agent/agreement/',
            'uuid'           => Str::uuid(),
        ];
    }
}
```

create:
```php
$agreementDTO = AgentAgreementDTO::createFromArray(
            [
                'user_id' => ApiUser::getUser()->getKey(),
                'bank_details' => ApiUser::getUser()
                    ->agentBankDetails->toArray(),
            ],
        );
```
